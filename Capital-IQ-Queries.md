Used for testing out Capital IQ

### Query IBM for All Estimate Data Points 
```
select GETDATE() as observeDate
, (select C.companyName from ciqCompany C where C.companyId = EP.companyId) as companyName
, (select EPT.periodTypeName from ciqEstimatePeriodType EPT where EPT.periodTypeId = EP.periodTypeId) as
periodTypeName
, EP.fiscalQuarter
, EP.fiscalYear
, EP.calendarQuarter
, EP.calendarYear
, (select DI.dataItemName from ciqdataitem DI where DI.dataitemid = ED.dataitemid) as dataItemName
, (select Cu.ISOCode from ciqCurrency Cu where Cu.currencyId = ED.currencyId) as ISOCode
, (select EST.estimateScaleName from ciqEstimateScaleType EST where EST.estimateScaleId =
ED.estimateScaleId) as estimateScaleName
, ED.dataItemValue,ED.effectiveDate
, DI.relDataItemId
, DI.dataItemId
, EN.noteText
from ciqEstimatePeriod EP
join ciqEstimateConsensus EC
on EC.estimatePeriodId = EP.estimatePeriodId
join ciqEstimateNumericData ED
on ED.estimateConsensusId = EC.estimateConsensusId
join ciqDataItemEstimateRel DI
on ED.dataItemId = DI.relDataItemId
and DI.dataItemRelTypeId = 4
left outer join ciqEstimateConsensusNote EN
on EC.estimateConsensusId = EN.estimateConsensusId
and DI.dataItemId = EN.dataItemId
and GETDATE() BETWEEN EN.effectiveDate and EN.toDate
where EP.companyId = 112350 -- IBM
and EP.periodTypeId = 2 -- annual
and GETDATE() between ED.effectiveDate and ED.toDate
```

### Query All of IBM's Estimates for 6/30 - (This will show the different estimate types like EBIT, EBIT, Net Income)
```
select
(select C.companyName from ciqCompany C where C.companyId = EP.companyId) as companyName
, (select EPT.periodTypeName from ciqEstimatePeriodType EPT where EPT.periodTypeId = EP.periodTypeId) as periodTypeName
, EP.fiscalYear
, (select DI.dataItemName from ciqdataitem DI where DI.dataitemid = ED.dataitemid) as dataItemName
, EC.tradingItemId
, (select Cu.ISOCode from ciqCurrency Cu where Cu.currencyId = ED.currencyId) as ISOCode
, (select EAS.accountingStandardDescription from dbo.ciqEstimateAccountingStd EAS where EAS.accountingStandardId = EC.accountingStandardId) as AccountingStandard
, (select EST.estimateScaleName from ciqEstimateScaleType EST where EST.estimateScaleId = ED.estimateScaleId) as estimateScaleName
, ED.dataItemValue,ED.effectiveDate
from ciqEstimatePeriod EP
--- link the core estimate table to data table
-----------------------------------------------------------
join ciqEstimateConsensus EC on EC.estimatePeriodId = EP.estimatePeriodId
join ciqEstimateNumericData ED on ED.estimateConsensusId = EC.estimateConsensusId
-----------------------------------------------------------
--- using a left join in order to retrieve company-level estimates that do not have a tradingitemID
-----------------------------------------------------------
left join ciqTradingItem TI on TI.tradingItemId = EC.tradingItemId
left join ciqSecurity S on S.securityId = TI.securityId
-----------------------------------------------------------
where EP.companyId = 112350 -- IBM
and EP.periodTypeId = 1 -- annual
and '6/30/2015' between ED.effectiveDate and ED.toDate
order by EP.fiscalYear desc, ED.dataItemId
```

### See all the queries made for a certain data type (and include revisions of up and down) for IBM on 6/30
```
select cep.companyid
, cc.companyname
,cti.tradingItemId
,ex.exchangeName
, cept.periodtypename
, cep.fiscalyear
,cep.fiscalQuarter
, cep.periodenddate
, cep.advancedate
, cend.dataitemid
, cdi.dataitemname
, cend.dataitemvalue
, cend.effectivedate
, cend.toDate
from ciqestimatePeriod cep
join ciqestimateConsensus cec on cep.estimateperiodid = cec.estimateperiodid
join ciqestimatenumericdata cend on cec.estimateconsensusid = cend.estimateconsensusid
join ciqcompany cc on cep.companyid = cc.companyid
join ciqSecurity cs on cs.companyId = cc.companyId
join ciqTradingItem cti on cti.securityId = cs.securityId and cti.tradingItemId = cec.tradingItemId
join ciqExchange ex on ex.exchangeId = cti.exchangeId
join ciqestimateperiodtype cept on cep.periodtypeid = cept.periodtypeid
join ciqdataitem cdi on cend.dataitemid = cdi.dataitemid
where cep.companyid = '112350' ---IBM
and cend.dataitemid in (100278,100280)
and cep.periodtypeid = '2' --Quarterly
and cs.primaryFlag = 1
and cti.primaryFlag = 1
and cep.periodEndDate = '6/30/2015'
order by cend.effectiveDate desc, cend.dataItemId
```