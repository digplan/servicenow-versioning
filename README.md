# servicenow-versioning
Diff customzations across ServiceNow instances

A collection of customizations is defined, called an *InstanceState*. This can be used to compare updated records to other instances.  

###InstanceState is a json object with the following keys:

    sn_instance: instance name
    collection: object with keys (tables) and values (encoded query)
    artifacts: object with keys (tablename) " " + sysid of record), and values (UTC time of last updated)
    table_hashes: Object with tablename + " " + number of artifacts, and values HASH of artifcats
    collection_hash, a HASH of the collection, in 8 characters
    version_hash, HASH of the artifacts, in 8 characters

For instance, a collection may be defined like this, which will capture all the records updated by *billd*.
````
{
  "collection": {
    "sys_script_include": "sys_updated_byLIKEbilld",
    "sys_script": "sys_updated_byLIKEbilld"
  }
}
````

This is run against one or more ServiceNow instances and generates the customer updates for that instance.  For instance, the result for instance1 is:
````
{
  "sn_instance": "instancename",
  "collection": {
    "sys_script_include": "sys_updated_byLIKEbilld",
    "sys_script": "sys_updated_byLIKEbilld"
  }
  "artifacts": {
    "sys_script_include 13903ddf1100c30006877e776144b0d2": "2017-07-24 22:04:27",
    "sys_script 15498683130df60048e3b7a66144b0ef": "2017-10-06 13:34:57"
  }
  "table_hashes": {
    "sys_script": "CD12098 1",
    "sysevent_in_email_action": "6EC4A20 1"
  },
  "collection_hash": "9339766",
  "version_hash": "8D7B65D"
}
````
Collection is the records included in scope.  A hash is simply the result of new GlideChecksum(tblhash)).getMD5().    
