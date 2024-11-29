public class OpportunityLineItemHandler {
    public static void NoOfOpportunityLineItemOnAccount(List<OpportunityLineItem> OpportunityLineItemList){
        Set<Id> oppId = new Set<Id>();
        for(OpportunityLineItem obj : OpportunityLineItemList){
            oppId.add(obj.OpportunityId);
        }
        Set<Id> accIds = new Set<Id>();
        for(Opportunity Opp : [SELECT Id , AccountId FROM Opportunity WHERE Id IN : oppId]){
            accIds.add(Opp.AccountId);
        }
        List<Account> accList = new List<Account>();
        List<OpportunityLineItem> newOpportunityLineItemList = new List<OpportunityLineItem>();
        Integer count = 0;
        for(Account acc : [SELECT Id , Name , (SELECT Id , AccountId FROM Opportunities) FROM Account WHERE Id IN : accIds]){
                                for(Opportunity opp : acc.Opportunities){
                                    count = count + [SELECT COUNT() FROM OpportunityLineItem WHERE OpportunityId =: opp.Id];
                                }
                                acc.Description = String.ValueOf(count);
                                accList.add(acc);
                            } 
        if(accList.size()>0){
            update accList;
        }
    }
}
