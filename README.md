# servicenow-versioning
Managing non-application customzations across instances

A collection of customizations (updated records in any table) is defined.

*Collection* is a json object with the following keys:
sn_instance: instance name
collection: object with keys (tables) and values (encoded query)
artifacts: object with keys (tablename, space, sysid of record) and values (UTC time of last updated)
collection_hash, a hash of the collection, in 8 characters
version_hash, a hash of the artifacts, in 8 characters

Collection and Version hashes can be compared across instances.

The tables are queried for their records' sysids and sys_updated_on values.
````
{
  "sn_instance": "instancename",
  "collection": {
    "sys_script_include": "name=DiscoverySensor"
  },
  "artifacts": {
    "sys_script_include da46a64c1323878848e8b7a66144b009": "2018-01-04 23:30:07"
  },
  "collection_hash": "713D7050",
  "version_hash": "23B4CCCC"
}
````

Reference implementation
````
var collection = {
  sys_script_include: 'name=DiscoverySensor'
};

var calc = calculateVersionInfo(collection, 'instance_name');
gs.log(JSON.stringify(calc, null, 2));

function calculateVersionInfo(coll, instance){
  var info = { sn_instance: instance, collection: coll, artifacts: {} };

  for(var table in coll){
	var gr = new GlideRecord(table); 
	gr.addEncodedQuery(coll[table]);
    gr.query();
    while(gr.next())
      info.artifacts[gr.getTableName() + ' ' + gr.getValue('sys_id')] = gr.sys_updated_on.getValue();
  }
  info.collection_hash = (new GlideChecksum(JSON.stringify(info.collection))).getMD5().slice(0, 8).toUpperCase();
  info.version_hash = (new GlideChecksum(JSON.stringify(info.artifacts))).getMD5().slice(0, 8).toUpperCase();

  return info;
}
````
