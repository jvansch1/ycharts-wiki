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
order by 5, 8, 4
```