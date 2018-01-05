# servicenow-versioning
Managing non-application customzations across instances

A collection of customizations is defined, with an associated has value that specifies its version. This can be compared across instances. 

Collection is a json object with three keys:
tables, containing an object with key of table name and stringarray of strings. each string is a table name and encoded query separated by a space
collection_hash, a hash of the tables array
version_hash, 

The tables are queried for their records' sysids and sys_updated_on values.

{
  collection: 'qw1231whs283a10923a2qwij281qll12',  // hash of all tables and queries concatenated
  version: '211wiu93uw99pq0on34se2qjks99knw',      // hash of all artifact values concatenated
  sn_instance: 'dev20123',
  
  sys_script: {
       'query': 'sys_updated_byNOTLIKEsystem',
       'artifacts': {
          'ww8rd3jd2wed28d3n7wqs8iwjjs9l0k 2017-12-25T11:45:00Z',
          'qq18dek8sw88kloppqwsdj3ksm7ki33m 2016-09-20T07:11:11Z'
       }
  }
}

Reference implementation
''''
var collection = {
  sys_script: 'nameLIKEdosomething'
}

function calculateVersionInfo(coll, instance){
  var info = { sn_instance: instance };
  var colldata = '';
  var vdata = '';
  
  for(table in coll){
    colldata += table + coll[table];
    
    var gr = new GlideRecord(table);
    gr.addEncodedQuery(coll[table]);
    gr.query();
    while(gr.next())
      vdata += gr.sys_id + isodate(gr.sys_updated_on);
  }
  var hasher = new GLIDEHASH();
  info.collection = hasher.hash(collinfo);
  info.version = hasher.hash(vdata);
  return info;
}
''''
