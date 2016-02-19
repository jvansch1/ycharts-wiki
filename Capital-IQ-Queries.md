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
and EP.fiscalYear between 2008 and 2017
```
![Test](http://d.pr/i/1laAU)
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