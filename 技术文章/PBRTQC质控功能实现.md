---
title: PBRTQC
date: 2023/10/08
categories:
  - coding
tags:
  - PBRTQC
  - 技术文章
---
# 1、基本概念

## 1.1、PBRTQC

基于患者样本的实时质量控制（patient-based real-time quality control，PBRTQC）系统是一种基于统计学以及数学模型的质量控制方法，是根据患者样本检测结果，利用统计学模型建立的一套以实时监测实验室检测质量的模型或者规则，是医学检验实验室提高质量控制体系的重要发展方向，最早由Hoffmann和Waid于1965年提出

## 1.2、PBRTQC模型

1. 五个关键参数：上截断值（UTL）、下截断值（LTL）、浮动窗口大小（N）、上控制限（UCL）、下控制限（LCL）
2. 排除规则：对于偏态分布以及存在极端值的分布数据，应选择一个适宜的截断值区间，将在截断值区间外的数据进行缩尾或者去除
3. SPC算法：浮动均值（MA）、浮动分位数（MQ）、指数加权移动平均值（EWMA）用于监测定值误差（CE）和百分比误差（PE）；浮动标准差（MovSD）、浮动非正常值患者数（MovSO）用于监测随机误差（RE）
4. 控制限：1. 分布法：通过分析历史数据生成的PBRTQC计算值，得出计算值的均值与标准差，根据正态分布或其它分布特征计算出对应的控制限
           2. 百分位点法：根据目标假阳性报警率（DFAR），如0.1%，考虑双边的情况下，那么控制限则为历史数据PBRTQC计算值的0.05%和99.95%百分位数点

## 1.3、模型评估指标
1. 假阳性报警率（FAR）：指误报次数与样本数的比值，文献中通常要求该值≤0.1%，即每进行1000例患者样本的实时质控，最多允许1次误报，表征模型的特异性，FAR越小，模型特异性越好
2. 误差检出前平均患者样本数（ANPed）：误差出现时至模型检测出误差所经历的患者样本例数，表征模型的灵敏度，例数越少，模型灵敏度越高
3. 二者关系：二者之间存在矛盾关系，FAR越低，ANPed越大，实际应用中应综合考虑，能满足当前项目的需求即可

# 2、实现简要流程

1. 数据类型位信号量，函数模型修改为mq，mq分位数设置为50,转换方式修改为不转换
2. 根据条件查询数据集合(仪器+批号+项目+时间)
3. 定量定性项目过滤，定量项目过滤掉单位为S/CO，定性项目液体类型为C（校准）或单位为S/CO
4. cut-off过滤（不包括校准点数据）
5. 根据washStatus判断是否需要排除修改前后信号值不一致的数据
6. 得到两份数据。allDataList：经过上述步骤剩余的数据。list：经过上述步骤再过滤掉校准点（液体类型C）数据
7. list为空、list大于20000，list大小小于训练数据集样本容量n。直接返回
8. 寻找定标线。液体类型为c（校准）,连续12个点确定一个定标线（不连续的舍弃）
9. 暂存list
10. 遍历函数模型集合，一个模型出一个图
11. 暂存全量数据list
12. 计算控制线
13. 每次移动训练数据集个数个数据。计算截断限LTL和UTL（通过移动分位数mq计算）
14. 根据LTL和UTL截断sublist数据。数据被截断并且是定标点，将前一个点设置为定标点
15. 数据转换sublist，使数据更符合正态性(0-Box-Cox变换、1-对数变换、2-倒数变换、3-平方根变换)
16. 记录最优lambda，加权失控报警0.5N和2N最优lambda用N计算出的lambda
17. 计算本次控制线（LCL,UCL）
18. 判断控制线的合理性：根据控制线偏差和Moving-Slope斜率最大值，最小值判断（斜率判断最新版本已废除）
19. 满足合理性判断时，暂存N转换后的数据,用作失控报警中的加权失控计算（0.5N，2N）
20. 未找到合适控制线时，直接返回并提示用户
21. 使用计算控制线得到的LTL和UTL截断全量数据，得到list
22. list数据转换(0-Box-Cox变换、1-对数变换、2-倒数变换、3-平方根变换)
23. 暂存转换后的数据,用作失控报警中的加权失控计算
24. list统计值计算(0-MA、1-MQ、2-EWMA、3-MovSD、4-MR)
25. Moving-Slope计算系统误差点（最新版本废弃）
26. 失控报警：失控点计算(0-单点失控,1-连续X点失控,2-加权失控，3-加权连续失控)
27. 找到所有定标线
28. 响应封装

# 3、代码实现

## 3.1、基本流程框架

```java
public AjaxResult pbrtqcLineData(PbrtqcDTO dto) {  
    PBRTQCResultVO response = new PBRTQCResultVO();  
    // 请求转换  
    if (dto.getDataType() == 1) {  
        // 数据类型位信号量，函数模型修改为mq，mq分位数设置为50,转换方式修改为不转换  
        dto.setFuncModel(1);  
        dto.setMqQuantile(50.0);  
        dto.setTransType(null);  
    }  
    // 根据条件查询数据集合(仪器+批号+项目+时间)  
    List<PbrtqcWashed> allDataList = iPbrtqcWashedService.selectLineData(dto);  
    // 定量项目&定性项目过滤  
    if (dto.getProjectType() == 0){  
        allDataList = allDataList.stream().filter(item -> !"S/CO".equals(item.getUnit())).collect(Collectors.toList());  
    }else {  
        allDataList = allDataList.stream().filter(item -> "C".equals(item.getLiquidType()) || "S/CO".equals(item.getUnit())).collect(Collectors.toList());  
    }  
    // cut-off过滤(不包括校准点数据)  
    if (1 == dto.getCutOffCondition()){  
        allDataList = allDataList.stream().filter(item -> "C".equals(item.getLiquidType()) ||  item.getValue() < dto.getCutOffValue()).collect(Collectors.toList());  
    }else if (2 == dto.getCutOffCondition()){  
        allDataList = allDataList.stream().filter(item -> "C".equals(item.getLiquidType()) ||  BigDecimal.valueOf(item.getValue()).compareTo(BigDecimal.valueOf(dto.getCutOffValue())) >= 0).collect(Collectors.toList());  
    }  
    // 不包含液体类型为C的数据 根据washStatus判断是否需要排除修改前后信号值不一致的数据，排除时不对C（校准点）排除  
    if (dto.getWashStatus() == 0){  
        allDataList = allDataList.stream().filter(item -> "C".equals(item.getLiquidType()) || item.getModifiedSignal().equals(item.getOriginalSignal())).collect(Collectors.toList());  
    }  
    List<PbrtqcWashed> list = allDataList.stream().filter(item -> !"C".equals(item.getLiquidType())).collect(Collectors.toList());  
    // 数据为空  
    if (CollectionUtils.isEmpty(list)) {  
        return AjaxResult.error("查询数据为空");  
    }  
    // 数据量过大不进行计算  
    if (list.size() > 20000) {  
        return AjaxResult.error("数据量过大（超过20000条），请调整查询条件");  
    }  
    // 训练数据集样本大于集合数量，调整数量  
    if (dto.getN() > list.size()) {  
        return AjaxResult.error("总数据量为：" + list.size() + ",小于训练数据集样本N，请调整N大小!");  
    }  
    // 寻找定标线  
    List<CalibrationPoint> calibrationDetailList = findCalibrationLine(allDataList); // 定标点集合  
    // 暂存全量数据list  
    List<PbrtqcWashed> tempList = saveDataList(list);  
    // 单位记录  
    String unit = list.get(0).getUnit();  
    List<PBRTQCData> pbrtqcDataList = new ArrayList<>();  
    List<Integer> funcModels = Arrays.stream(dto.getFuncModels().split(",")).map(Integer::parseInt).collect(Collectors.toList());  
    // 把movsd放到集合最后一个元素  
    for (int i=0; i< funcModels.size(); i++){  
        if (funcModels.get(i) == 3){  
            funcModels.remove(i);  
            funcModels.add(3);  
        }  
    }  
    // 序号  
    dto.setIndex(0);  
    for (Integer funcModel : funcModels) {  
        List<PbrtqcWashed> modelDataList = saveDataList(list);  
        // 函数模型  
        dto.setFuncModel(funcModel);  
        // 控制限偏差设置  
        if (funcModel == 3) {  
            dto.setLimitQuantile(dto.getSdLimitQuantile());  
        } else {  
            dto.setLimitQuantile(dto.getMqLimitQuantile());  
        }  
        // 计算N控制限  
        ControlLineData controlLine = calculateControlLine(dto, tempList, 1, 0, dto.getIndex());  
        // 控制限判断  
        if ((dto.getIndex() ==  0 && controlLine.getUcl() == null) || (dto.getIndex() > 0 && dto.getFindControlLine() == null)){  
            return AjaxResult.error("未找到合适的控制线,请设置合理的控制限偏差");  
        }  
        List<PbrtqcWashed> cutDataList = modelDataList;  
        if (dto.getCutType() != 0) {  
            // LTL（下截断限值）和UTL（上截断限值）  
            QuantileData quantileData = new QuantileData(dto.getLtl(),dto.getUtl());  
            // 根据截断方式截断数据  
            cutDataList = cutData(dto, quantileData, modelDataList);  
        }  
        // 数据转换(0-Box-Cox变换、1-对数变换、2-倒数变换、3-平方根变换)  
        dataTrans(dto, cutDataList, 0);  
        // 暂存转换后的数据,用作失控报警中的加权失控计算  
        List<PbrtqcWashed> transData = new ArrayList<>();  
        if (dto.getAlarmMode() == 2 || dto.getAlarmMode() == 3){  
            transData = saveDataList(cutDataList);  
        }  
        // 统计值计算(0-MA、1-MQ、2-EWMA、3-MovSD、4-MR)  
        List<PBRTQCLineVO> dataList = funcModelCalculate(dto, cutDataList,1);  
        // Moving-Slope计算系统误差失控点  
        List<PBRTQCLineVO> systemErrorDataList = systemErrorPoint(dto, dataList);  
        // 失控报警  
        List<PBRTQCLineVO> alarmDataList = outOfControlAlarm(dto, tempList, dataList, controlLine, transData);  
        // 批号计算  
        List<String> lots = dataList.stream().map(PBRTQCLineVO::getName).distinct().collect(Collectors.toList());  
        lots.add(0, "失控点");  
        lots.add(1, "系统误差点");  
        // 设置失控点  
        alarmDataList.forEach(item -> dataList.get(item.getId()).setName("失控点"));  
        // 找到所有的定标线  
        List<PBRTQCLineVO> calibrationPointList = dataList.stream().filter(item ->  
                item.getIsCalibration() != null && item.getIsCalibration().equals(1)).collect(Collectors.toList());  
        List<Integer> calibrationList = calibrationPointList.stream().map(PBRTQCLineVO::getId).collect(Collectors.toList());  
        // 寻找失控点中的所有系统误差点  
        List<PBRTQCLineVO> systemErrorData = alarmDataList.stream().filter(item -> systemErrorDataList.stream().anyMatch(obj -> obj.getId().equals(item.getId()))).collect(Collectors.toList());  
        systemErrorData.forEach(item -> dataList.get(item.getId()).setName("系统误差点"));  
        // 响应封装  
        PBRTQCData data = new PBRTQCData();  
        data.setList(dataList);  
        data.setLots(lots);  
        data.setUnit(unit);  
        data.setMethodName(DictUtils.getDictLabel("ims_pbrtqc_func_model", dto.getFuncModel().toString()));  
        data.setAlarmList(alarmDataList);  
        data.setSystemErrorList(systemErrorData);  
        data.setLcl(controlLine.getLcl());  
        data.setUcl(controlLine.getUcl());  
        data.setOriginLcl(controlLine.getOriginLcl());  
        data.setOriginUcl(controlLine.getOriginUcl());  
        data.setCalibrationList(calibrationList);  
        pbrtqcDataList.add(data);  
        dto.setIndex(dto.getIndex() + 1);  
    }  
    response.setList(pbrtqcDataList);  
    response.setLtl(dto.getLtl());  
    response.setUtl(dto.getUtl());  
    response.setCalibrationPointList(calibrationDetailList);  
    return AjaxResult.success(response);  
}
```