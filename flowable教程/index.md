# Flowable教程


[//]: # (![集合]&#40;/img/集合/img.png&#41;)

[Flowable BPMN 用户手册](https://tkjohn.github.io/flowable-userguide/#bpmnJavaServiceTask)

## 方法工具类

### 流程图

![1](/img/flowable教程/1.png)

![2](/img/flowable教程/2.png)

### 启动流程

```java
// 启动流程
Map<String, Object> variables = new HashMap<>();
remoteFlowAbleService.startProcessByCodeAndId(ProcessConstants.XXXXXX, id, variables);
```

* ProcessConstants.XXXXXX 流程类型
* id 业务id
* variables 流程变量

```java
    /**
     * 根据流程类型和业务ID启动流程
     *
     * @param code
     * @param id
     */
    @Override
    @Transactional(rollbackFor = Exception.class)
    public WfTaskVo startProcessByCodeAndId(String code, Long id, Map<String, Object> variables) {
        ProcessDefinition procDef = repositoryService.createProcessDefinitionQuery()
                .processDefinitionCategory(code).latestVersion().singleResult();
        try {
            if (ObjectUtil.isNotNull(procDef) && procDef.isSuspended()) {
                throw new ServiceException("流程已被挂起，请先激活流程");
            }
            // 设置流程发起人Id到流程中
            String userIdStr = TaskUtils.getUserId();
            identityService.setAuthenticatedUserId(userIdStr);
            variables.put(BpmnXMLConstants.ATTRIBUTE_EVENT_START_INITIATOR, userIdStr);
            // 设置流程状态为进行中
            variables.put(ProcessConstants.PROCESS_STATUS_KEY, ProcessStatus.RUNNING.getStatus());
            // 发起流程实例
            ProcessInstance processInstance = runtimeService.startProcessInstanceById(procDef.getId(), String.valueOf(id), variables);
            //processInstance无法经controller层Jason化返回，构建返回vo
            WfTaskVo vo = new WfTaskVo();
            vo.setBusinessKey(processInstance.getBusinessKey());
            vo.setProcInsId(processInstance.getProcessInstanceId());
            return vo;
        } catch (Exception e) {
            e.printStackTrace();
            throw new ServiceException("流程启动错误");
        }
    }
```

### 审核流程

```java
        WfTaskBo wfTask = new WfTaskBo();
        wfTask.setVariables(info.getVariables());
        wfTask.setBusinessKey(info.getId());
        wfTask.setComment(info.getContent());
        // 走流程
        remoteFlowAbleService.completeTaskByBusId(wfTask);
```

* info.getVariables() 流程变量
* info.getId() 业务id
* info.getContent() 审核意见

```java
/**
 * 根据业务ID完成当前用户的任务
 */
@Override
@Transactional(rollbackFor = Exception.class)
public void completeTaskByBusId(WfTaskBo info) {
        Map<String, Object> variables = info.getVariables();
        Task task = taskService.createTaskQuery()
                    .processInstanceBusinessKey(String.valueOf(info.getBusinessKey()))
                    .singleResult();
        if (Objects.isNull(task)) {
        throw new ServiceException("任务不存在");
        }
        // 获取 bpmn 模型
        BpmnModel bpmnModel = repositoryService.getBpmnModel(task.getProcessDefinitionId());
        if (DelegationState.PENDING.equals(task.getDelegationState())) {
            if (StringUtils.isNotEmpty(info.getComment())) {
            taskService.addComment(task.getId(), task.getProcessInstanceId(), FlowComment.DELEGATE.getType(), info.getComment());
            }
            taskService.resolveTask(task.getId());
            } else {
            if (StringUtils.isNotEmpty(info.getComment())) {
             // 添加审批意见
                taskService.addComment(task.getId(), task.getProcessInstanceId(), FlowComment.NORMAL.getType(), info.getComment());
            }
        taskService.setAssignee(task.getId(), TaskUtils.getUserId());
        if (ObjectUtil.isNotEmpty(variables)) {
        // 获取模型信息
        String localScopeValue = ModelUtils.getUserTaskAttributeValue(bpmnModel, task.getTaskDefinitionKey(), ProcessConstants.PROCESS_FORM_LOCAL_SCOPE);
        boolean localScope = Convert.toBool(localScopeValue, false);
        taskService.complete(task.getId(), variables, localScope);
        } else {
        taskService.complete(task.getId());
        }
        }
}
```

### 结束流程

```java
String procInstId = remoteFlowAbleService.getProcessInstanceIdById(String.valueOf(id)).getMsg();
WfTaskBo delBo = new WfTaskBo();
delBo.setProcInsId(procInstId);
remoteFlowAbleService.stopProcess(delBo);
```
* getProcessInstanceIdById 根据业务id获取流程实例id
* procInstId 流程实例id

```java
    /**
     * 根据id获取ProcessInstanceId
     *
     * @param id
     * @return
     */
    @Override
    public String getProcessInstanceIdById(String id) {
        ProcessInstance processInstance = runtimeService.createProcessInstanceQuery()
                .processInstanceBusinessKey(id).singleResult();
        String procInstId = processInstance.getProcessInstanceId();
        return procInstId;
    }
```

```java
    /**
     * 取消申请
     *
     * @param bo
     * @return
     */
    @Override
    public void stopProcess(WfTaskBo bo) {
        List<Task> task = taskService.createTaskQuery().processInstanceId(bo.getProcInsId()).list();
        if (CollectionUtils.isEmpty(task)) {
            throw new RuntimeException("流程未启动或已执行完成，取消申请失败");
        }

        ProcessInstance processInstance = runtimeService.createProcessInstanceQuery()
                .processInstanceId(bo.getProcInsId()).singleResult();
        BpmnModel bpmnModel = repositoryService.getBpmnModel(processInstance.getProcessDefinitionId());
        if (Objects.nonNull(bpmnModel)) {
            Process process = bpmnModel.getMainProcess();
            List<EndEvent> endNodes = process.findFlowElementsOfType(EndEvent.class, false);
            if (CollectionUtils.isNotEmpty(endNodes)) {
                Authentication.setAuthenticatedUserId(TaskUtils.getUserId());
//                taskService.addComment(task.getId(), processInstance.getProcessInstanceId(), FlowComment.STOP.getType(),
//                        StringUtils.isBlank(flowTaskVo.getComment()) ? "取消申请" : flowTaskVo.getComment());
                // 获取当前流程最后一个节点
                String endId = endNodes.get(0).getId();
                List<Execution> executions = runtimeService.createExecutionQuery()
                        .parentId(processInstance.getProcessInstanceId()).list();
                List<String> executionIds = new ArrayList<>();
                executions.forEach(execution -> executionIds.add(execution.getId()));
                // 变更流程为已结束状态
                runtimeService.createChangeActivityStateBuilder()
                        .moveExecutionsToSingleActivityId(executionIds, endId).changeState();
            }
        }
    }
```

### 删除流程实例

* 需要先结束流程再删除

```java
remoteFlowAbleService.deleteProcessByIds(procInstId);
```

```java
String[] instanceIds = new String[1];
instanceIds[0] = procInstId;
iWfProcessService.deleteProcessByIds(instanceIds);
```

```java
    /**
     * 删除流程
     * @param instanceIds
     */
    @Override
    @Transactional(rollbackFor = Exception.class)
    public void deleteProcessByIds(String[] instanceIds) {
        List<String> ids = Arrays.asList(instanceIds);
        // 校验流程是否结束
        long activeInsCount = runtimeService.createProcessInstanceQuery()
            .processInstanceIds(new HashSet<>(ids)).active().count();
        if (activeInsCount > 0) {
            throw new ServiceException("不允许删除进行中的流程实例");
        }
        // 删除历史流程实例
        historyService.bulkDeleteHistoricProcessInstances(ids);
    }
```

### 获取自己拥有的流程业务ID

```java
List<Long> ownIds = remoteFlowAbleService.getOwnProcessBusinessIds(ProcessConstants.XXXXXX).getData();
```

* ProcessConstants.XXXXXX 流程类型

```java
    /**
     * 获取自己拥有的流程业务ID
     *
     * @param code
     * @return
     */
    @Override
    @Transactional(rollbackFor = Exception.class)
    public List<Long> getOwnProcessBusinessIds(String code) {
        List<Long> ids = new ArrayList<>();
        ProcessQuery processQuery = new ProcessQuery();
        HistoricProcessInstanceQuery historicProcessInstanceQuery = historyService.createHistoricProcessInstanceQuery()
                .startedBy(TaskUtils.getUserId())
                .processDefinitionCategory(code)
                .orderByProcessInstanceStartTime()
                .desc();
        // 构建搜索条件
        ProcessUtils.buildProcessSearch(historicProcessInstanceQuery, processQuery);
        Long count = historicProcessInstanceQuery.count();
        List<HistoricProcessInstance> historicProcessInstanceList = historicProcessInstanceQuery.list();
        for (HistoricProcessInstance hisIns : historicProcessInstanceList) {
            // 获取业务数据ID（即业务主键）
            String businessDataId = hisIns.getBusinessKey();
            if (!Constants.NULL.equals(businessDataId) || StringUtils.isNotBlank(businessDataId)) {
                ids.add(Long.valueOf(businessDataId));
            }
        }
        return ids;
    }
```

### 获取待办任务对应的业务ID

```java
List<Long> ids = remoteFlowAbleService.getUserBusinessIds(ProcessConstants.XXXXXX).getData();
```

* ProcessConstants.XXXXXX 流程类型

```java
    /**
     * 获取待办任务对应的业务ID
     *
     * @param code 流程分类标识
     * @return
     */
    @Override
    @Transactional(rollbackFor = Exception.class)
    public List<Long> getUserBusinessIds(String code) {
        List<Long> ids = new ArrayList<>();
        ProcessQuery processQuery = new ProcessQuery();
        TaskQuery taskQuery = taskService.createTaskQuery()
                .active()
                .includeProcessVariables()
                .taskCandidateOrAssigned(TaskUtils.getUserId())
                .taskCandidateGroupIn(TaskUtils.getCandidateGroup())
                .orderByTaskCreateTime().desc();
        // 构建搜索条件
        ProcessUtils.buildProcessSearch(taskQuery, processQuery);
        Long count = taskQuery.count();
        List<Task> taskList = taskQuery.list();
        for (Task task : taskList) {
            // 获取流程实例ID
            String processInstanceId = task.getProcessInstanceId();

            ProcessInstance processInstance = runtimeService
                    .createProcessInstanceQuery()
                    .processInstanceId(processInstanceId)
                    .processDefinitionCategory(code)
                    .singleResult();

            if (processInstance != null) {
                // 获取业务数据ID（即业务主键）
                String businessDataId = processInstance.getBusinessKey();
                if (StringUtils.isNotEmpty(businessDataId)) {
                    ids.add(Long.valueOf(businessDataId));
                }
            }
        }
        return ids;
    }
```

### 获取所有已办流程任务

```java
// 获取所有已办流程任务
List<WfTaskVo> allWfTaskVo = remoteFlowAbleService.customGetAllProcessTasksbyType(bo.getProcessName()).getData();
```

* bo.getProcessName() 流程名称

```java
    /**
     * 获取所有已办流程任务
     *
     * @param processName
     * @return
     */
    @Override
    public List<WfTaskVo> customGetAllProcessTasksbyType(String processName) {
        HistoricTaskInstanceQuery taskInstanceQuery = historyService.createHistoricTaskInstanceQuery()
                .includeProcessVariables()
                .finished()
                .orderByHistoricTaskInstanceEndTime()
                .desc();
        if (!SecurityUtils.getLoginUser().getSysUser().isAdmin()) {
            taskInstanceQuery = taskInstanceQuery.taskAssignee(TaskUtils.getUserId());
        }
        // 构建搜索条件
        ProcessQuery processQuery = new ProcessQuery();
        processQuery.setProcessName(processName);
        ProcessUtils.buildProcessSearch(taskInstanceQuery, processQuery);
        List<HistoricTaskInstance> historicTaskInstanceList = taskInstanceQuery.list();

        List<WfTaskVo> hisTaskList = new ArrayList<>();

        for (HistoricTaskInstance histTask : historicTaskInstanceList) {

            WfTaskVo flowTask = new WfTaskVo();
            // 当前流程信息
            flowTask.setTaskId(histTask.getId());
            // 审批人员信息
            flowTask.setCreateTime(histTask.getCreateTime());
            flowTask.setFinishTime(histTask.getEndTime());
            flowTask.setDuration(DateUtil.formatBetween(histTask.getDurationInMillis(), BetweenFormatter.Level.SECOND));
            flowTask.setProcDefId(histTask.getProcessDefinitionId());
            flowTask.setTaskDefKey(histTask.getTaskDefinitionKey());
            flowTask.setTaskName(histTask.getName());

            // 流程定义信息
            ProcessDefinition pd = repositoryService.createProcessDefinitionQuery()
                    .processDefinitionId(histTask.getProcessDefinitionId())
                    .singleResult();
            flowTask.setDeployId(pd.getDeploymentId());
            flowTask.setProcDefName(pd.getName());
            flowTask.setProcDefVersion(pd.getVersion());
            flowTask.setProcInsId(histTask.getProcessInstanceId());
            flowTask.setHisProcInsId(histTask.getProcessInstanceId());

            // 流程发起人信息
            HistoricProcessInstance historicProcessInstance = historyService.createHistoricProcessInstanceQuery()
                    .processInstanceId(histTask.getProcessInstanceId())
                    .singleResult();
            Long userId = Long.parseLong(historicProcessInstance.getStartUserId());
            String nickName = remoteUserService.selectNickNameById(userId);
            flowTask.setStartUserId(userId);
            flowTask.setStartUserName(nickName);
            // 业务ID
            flowTask.setBusinessKey(historicProcessInstance.getBusinessKey());

            // 流程变量
            flowTask.setProcVars(histTask.getProcessVariables());

            hisTaskList.add(flowTask);
        }
        return hisTaskList;
    }
```



## 表结构说明

[参考链接](https://www.cnblogs.com/phyger/p/14067201.html)

```text
ACT_RE_*      :      ’RE’表示repository（存储）。RepositoryService接口操作的表。带此前缀的表包含的是静态信息，如，流程定义，流程的资源（图片，规则等）。

ACT_RU_*      :      ’RU’表示runtime。这是运行时的表存储着流程变量，用户任务，变量，职责（job）等运行时的数据。flowable只存储实例执行期间的运行时数据，当流程实例结束时，将删除这些记录。这就保证了这些运行时的表小且快。

ACT_ID_*      :      ’ID’表示identity(组织机构)。这些表包含标识的信息，如用户，用户组，等等。

ACT_HI_*      :      ’HI’表示history。就是这些表包含着历史的相关数据，如结束的流程实例，变量，任务，等等。

ACT_GE_*      :      普通数据，各种情况都使用的数据。
```

## 问题

* flowable已经执行完毕的流程去哪找？

[参考链接](https://blog.51cto.com/u_9806927/5845891)










