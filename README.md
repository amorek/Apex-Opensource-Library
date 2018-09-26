# Salesforce Developer Toolkit
## Introduction


## [Collections](https://github.com/amorek/sfdc-toolkit)
This utility class contains methods for the most common operations on Salesforce collections:
- mapping by field
- groupping by field
- filtering
- finding
- sorting
- wrapping into wrapper classes
- filling lists with clones.
- others 

All methods in this class can be used as a wrapper on list instance and chained ala lambda:
```apex
        Map<Boolean, List<OpportunityWrapper2>> actual = (Map<Boolean, List<OpportunityWrapper2>>)
                new Collection(opportunities)
                        .filter(Opportunity.CreatedDate, '>=', Date.today().addDays(-3))
                        .filter(new CollectionFilters.ByFieldValues(Opportunity.StageName,'IN', new Set<Object>{'Analysis','Presales'}))
                        .forEach(new AppendIndexToOpportunityNameWorker())
                        .wrap(OpportunityWrapper2.class)
                        .groupBy(new WrapperByOpenActivityMapper());
```
or as static methods
```apex
    List<Account> accounts = Collection.filter(accounts, new TriggeredAccountsFilter());
    
    List<Account> relatedToParentIds = Collection.filter(accounts, new CollectionFilters.RelatedTo(parentAccounts, Account.ParentId));
```
Many of these methods are based on Javascript Array methods.

#### Methods:
##### getUniqueValues()
Return Set of values gathered from given collection items.
Values are gathered by implementation of KeyMapper, which extracts value from entity, but it's also possible
to use shorthand methods for SObjects, which call in-built implementations.

###### Examples
```apex
/*Static through SObject field*/
Set<String> accountNames = (Set<String>) Collection.getUniqueValues(accounts, Account.Name);

/*Through instance*/
Set<Datetime> accountCreatedDates = (Set<Datetime>) new Collection(accounts).getUniqueValues(Account.Datetime);

/*Through String field. Values type has to be provided.*/
Set<Id> parentAccountIds = (Set<Id>) Collection.getUniqueValues(Id.class, accounts, 'ParentId');

/*From non-SObject entities through KeyMapper interface implementation*/
Set<Id> parentAccountIds = (Set<Id>) new Collection(accountWrappers).getUniqueValues(new GetAccountWrapperIds());

/*From many fields*/
Set<Id> parentAccountIds = (Set<Id>) new Collection(accounts).getUniqueValues(new Set<SObjectField>{Account.ParentId, Account.Parent__c});

```



##### mapBy
Map collection items using: 
- KeyMapper implementation (Map's key is generated by KeyMapper, while collection item is the value)
- Mapper implementation (Both map's key and value are generated by Mapper)
- Shorthand methods which use in-built implementations:
    - Map by SObject field
    
Inbuilt implementations:
- CollectionMappers.ByField - Maps SObject by given field
- CollectionMappers.ByFieldPair - Maps SObject by pair of fields

###### Interfaces
```
    /**
     * KeyMapper implementations determine Map's key for given object and key's type.
     */
    public interface KeyMapper {
        Object key(Object item);
        Type keyType();
    }

    /**
     * Mapper implementations determine Map's key and value for given object and types of key and value.
     */
    public interface Mapper {
        Type keyType();
        Type valueType();
        Object key(Object item);
        Object value(Object item);
    }
```

###### Examples
```apex
/*Shorthand method to maps Accounts by Name field*/
Map<String, Account> accountByNames = (Map<String, Account>) Collection.mapBy(accounts, Account.Name);

/*Shorthand method to maps Accounts by Name field (given as string)*/
Map<String, Account> accountByNames = (Map<String, Account>) new Collection(accounts).mapBy(String.class, 'Name');

/** Maps by custom Mapper implementation, which generates both map's key and value*/
Map<Id, AccountWrapper> accountWrappersByParent = (Map<Id, AccountWrapper>) Collection.mapBy(accounts, new AccountWrapperByIdMapper());

private class AccountWrapperByIdMapper implements Mapper{
            public Type keyType() {
                return Id.class
            }
            public Type valueType() {
                return AccountWrapper.class;
            }
    
            public Object key(Object item) {
                return ((Account) item).Id;
            }
    
            public Object value(Object item) {
                return new AccountWrapper((Account) item);
            }
}
```


##### groupBy
Groups collection items by given Mapper/KeyMapper implementation to Map<Key, List<Value>>, where Key is produced
by Mapper and Map's value is list of collection items with the same map key.
This method can be used with:
- KeyMapper implementation (Map's key is generated by KeyMapper, while collection item is the value)
- Mapper implementation (Both map's key and value are generated by Mapper)
- Shorthand methods which use in-built implementations:
    - Map by SObject field
    
Inbuilt implementations:
- CollectionMappers.ByField - Maps SObject by given field
- CollectionMappers.ByFieldPair - Maps SObject by pair of fields
###### Examples
```apex
//Shorthand method using SObject field
Map<String, List<Opportunity>> actual = (Map<String, List<Opportunity>>) Collection.groupBy(opportunities, Opportunity.NextStep);
```



##### filter
###### Examples


##### find
###### Examples


##### sort()
###### Examples


##### wrap()
###### Examples


##### reduce()
###### Examples


##### forEach()
###### Examples


##### fill()
###### Examples


##### Utility methods
###### Examples

## Datatable
tbc
## XML Parser
tbc


You can use the [editor on GitHub](https://github.com/amorek/sfdc-toolkit/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.



### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/amorek/sfdc-toolkit/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
