USE retail_segmentation;
select * from txn inner join cust on txn.household_id = cust.household_id;

###	Step 1	Get the transaction table at unique customer level. Use the following template - 		

create table trntable as 
select household_id, 
(case when Home_Shopping_Flg = 0 then visits else 0 end) as totalvisits0,
(case when Home_Shopping_Flg = 0 then tot_spend else 0 end) as totalspend0,
(case when Home_Shopping_Flg = 0 then tot_qty else 0 end) as totalquantity0,
(case when Home_Shopping_Flg = 1 then visits else 0 end) as totalvisits1,
(case when Home_Shopping_Flg = 1 then tot_spend else 0 end) as totalspend1,
(case when Home_Shopping_Flg = 1 then tot_qty else 0 end) as totalquantity1,
(case when Home_Shopping_Flg = 1 or Home_Shopping_Flg = 0 then visits end) as totalvisits,
(case when Home_Shopping_Flg = 1 or Home_Shopping_Flg = 0 then tot_spend end) as totalspend,
(case when Home_Shopping_Flg = 1 or Home_Shopping_Flg = 0 then tot_spend end) as totalquantity,
(case when Home_Shopping_Flg = 0 then 'Instore'when Home_Shopping_Flg = 1 then 'Online'   
WHEN Home_Shopping_Flg = 1 and Home_Shopping_Flg = 0 then 'Multichannel' end)  as shoppingmodesegment 
from txn;

select*from trntable;

#####Step 2	Join the above table with Customer table. See that there are more records in Customer Table. Use inner join.				

 
SELECT * FROM trntable inner join cust on trntable.household_id = cust.household_id;
  
###Shopping mode Segment	Avg Total Visits	Avg Total Spends	Avg Total Quantity

select shoppingmodesegment, avg (tot_spend), avg(visits), avg(tot_qty)
from trntable inner join txn on txn.household_id = trntable.household_id group by shoppingmodesegment; 

###	Shopping mode Segment	Very Frequent Shoppers	Occasional Shoppers	No longer Shopping	Lapsing Shoppers
select round(100*sum(case when loyalty = 'Very Frequent Shoppers' then 1 else 0 end)/count(1),0)  as PercofVeryFrequentShoppers,
		round(100*sum(case when loyalty = 'Occasional Shoppers' then 1 else 0 end)/count(1),0)  as PercofOccasionalShoppers,
        round(100*sum(case when loyalty = 'No longer Shopping' then 1 else 0 end)/count(1),0)  as PercofNolongerShopping,
        round(100*sum(case when loyalty = 'Lapsing Shoppers' then 1 else 0 end)/count(1),0)  as PercofLapsingShoppers,
        shoppingmodesegment
	from cust inner join trntable on trntable.household_id=cust.household_id group by shoppingmodesegment; 	

###Shopping mode Segment	Very Large Stores	Large Stores	Others	Small Stores	Mom and Pop Stores
##Customers prefer large stores, company can open large stores.  

select round(100*sum(case when preferred_store_format = 'Very Large Stores' then 1 else 0 end)/count(1),0)  as PercofVeryLargeStores,
		round(100*sum(case when preferred_store_format = 'Large Stores' then 1 else 0 end)/count(1),0)  as PercofLargeStores,
        round(100*sum(case when preferred_store_format = 'Others' then 1 else 0 end)/count(1),0)  as PercofOthers,
        round(100*sum(case when preferred_store_format = 'Small Stores' then 1 else 0 end)/count(1),0)  as PercofSmallStores,
        round(100*sum(case when preferred_store_format = 'Mom and Pop Stores' then 1 else 0 end)/count(1),0)  as PercofMomandPopStores,
        shoppingmodesegment
	from cust inner join trntable on trntable.household_id=cust.household_id group by shoppingmodesegment; 


###	Shopping mode Segment	Middle Class	Low Affluent Customers	Very Affluent Customers
###Instore customers consist of every income class but 41% of online customers consist of middle class. 
#Company can build marketing strategy targeting middle class on the website.

select round(100*sum(case when lifestyle = 'Middle Class' then 1 else 0 end)/count(1),0)  as PercofMiddleClass,
		round(100*sum(case when lifestyle = 'Low Affluent Customers' then 1 else 0 end)/count(1),0)  as PercofLowAffluentCustomers,
        round(100*sum(case when lifestyle = 'Very Affluent Customers' then 1 else 0 end)/count(1),0)  as PercofVeryAffluentCustomers,
        shoppingmodesegment
	from cust inner join trntable on trntable.household_id=cust.household_id group by shoppingmodesegment; 

###	Shopping mode Segment	M	F	X
##significant percentage of customers consist of Females. Company can build promotions and marketing strategies targeting females
select round(100*sum(case when gender = 'M' then 1 else 0 end)/count(1),0)  as PercofM,
		round(100*sum(case when gender= 'F' then 1 else 0 end)/count(1),0)  as PercofF,
        round(100*sum(case when gender = 'X' then 1 else 0 end)/count(1),0)  as PercofX,
        shoppingmodesegment
	from cust inner join trntable on trntable.household_id=cust.household_id group by shoppingmodesegment; 

#### Step 4	Use Table 1 to create Table 2
#Total Visit Score

SELECT totalvisits, household_id,
(case when totalvisits > (sum(totalvisits)*66/100) then '3' 
when totalvisits < (sum(totalvisits)*33/100) then '1' 
when totalvisits > (sum(totalvisits)*33/100) and totalvisits < (sum(totalvisits)*66/100) then '2' end) as Totalvisitscore
from trntable group by household_id;

# Total Spend Score
select totalspend , household_id,
(case when totalspend	> (sum(totalspend )*66/100) then '3' 
when totalspend < (sum(totalspend )*33/100) then '1' 
 when totalspend  > (sum(totalspend )*33/100) and totalspend  < (sum(totalspend )*66/100) then '2' end) as totalspendscore
from trntable group by totalspend ;

###Total Score	Total Visit Score + Total Spend Score
###Company can build campaign targeting customers with low total score
SELECT totalspend, totalvisits,
 (case  when ((totalvisits+totalspend) > sum(totalvisits+totalspend)*66/100) then 'Champions'
 when ((totalvisits+totalspend) < sum(totalvisits+totalspend)*33/100) then 'Leggards'
 when ((totalvisits+totalspend) > sum(totalvisits+totalspend)*33/100) and totalspend  < (sum(totalspend )*66/100) then 'Potential' end) as TotalScore
from trntable group by household_id;

###Value Segment	Avg Total Visits	Avg Total Spends	Avg Total Quantity
### more visit means more spending. Company can build campaign to increase store visits targeting Leggards 
create table valuesegment as 
SELECT totalspend, totalvisits, totalquantity,
 (case  when ((totalvisits+totalspend) > sum(totalvisits+totalspend)*66/100) then 'Champions'
 when ((totalvisits+totalspend) < sum(totalvisits+totalspend)*33/100) then 'Leggards'
 when ((totalvisits+totalspend) > sum(totalvisits+totalspend)*33/100) and ((totalvisits+totalspend) < sum(totalvisits+totalspend)*66/100) then 'Potential' end) as TotalScore
from trntable group by household_id;

select*from valuesegment;
### more visit means more spending. Company can build campaign to increase store visits targeting Leggards
select avg(totalvisits), avg(totalspend),avg(totalquantity), TotalScore from valuesegment group by TotalScore;


###Value Segment	Very Frequent Shoppers	Occasional Shoppers	No longer Shopping	Lapsing Shoppers
select* from (select round(100*sum(case when loyalty = 'Very Frequent Shoppers' then 1 else 0 end)/count(1),0)  as PercofVeryFreqShoppers,
		round(100*sum(case when loyalty = 'Occasional Shoppers' then 1 else 0 end)/count(1),0)  as PercofOccasionalShoppers,
        round(100*sum(case when loyalty = 'No longer Shopping' then 1 else 0 end)/count(1),0)  as PercofNolongerShoppin,
        round(100*sum(case when loyalty = 'Lapsing Shoppers' then 1 else 0 end)/count(1),0)  as PercofLapsingShoppers
from (SELECT * FROM trntable inner join cust on trntable.household_id = cust.household_id)) as zzz
inner join valuesegment on valuesegment.totalvisits = zzz.totalvisits;

###Value Segment	Very Large Stores	Large Stores	Others	Small Stores	Mom and Pop Stores
select round(100*sum(case when preferred_store_format = 'Very Large Stores' then 1 else 0 end)/count(1),0)  as PercofVeryLargeStores,
		round(100*sum(case when preferred_store_format = 'Large Stores' then 1 else 0 end)/count(1),0)  as PercofLargeStores,
        round(100*sum(case when preferred_store_format = 'Others' then 1 else 0 end)/count(1),0)  as PercofOthers,
        round(100*sum(case when preferred_store_format = 'Small Stores' then 1 else 0 end)/count(1),0)  as PercofSmallStores,
        round(100*sum(case when preferred_store_format = 'Mom and Pop Stores' then 1 else 0 end)/count(1),0)  as PercofMomandPopStores,
    





 

 







