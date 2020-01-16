# Compute Service Endpoints

## Create new job or restart an existing stopped job

### POST /api/v1/compute


Start a new job

Parameters
```
{
  "workflow": {
    "agreementId":"111",
    "owner":"0x22",
    "stages": [
      {
        "index": 0,
        "input": [
          {
            "id": "did:op:87bdaabb33354d2eb014af5091c604fb4b0f67dc6cca4d18a96547bffdc27bcf",
            "url": [
              "https://data.ok.gov/sites/default/files/unspsc%20codes_3.csv",
              "https://gishubdata.nd.gov/sites/default/files/NDHUB.Roads_MileMarkers_1.csv"
            ],
            "index": 0
          },
          {
            "id": "did:op:1384941e6f0b46299b6e515723df3d8e8e5d1fb175554467a1cb7bc613f5c72e",
            "url": [
              "https://data.ct.gov/api/views/2fi9-sgi3/rows.csv?accessType=DOWNLOAD"
            ],
            "index": 1
          }
        ],
        "compute": {
          "Instances": 1,
          "namespace": "withgpu",
          "maxtime": 3600
        },
        "algorithm": {
          "id": "did:op:87bdaabb33354d2eb014af5091c604fb4b0f67dc6cca4d18a96547bffdc27bcf",
          "url": "https://raw.githubusercontent.com/oceanprotocol/test-algorithm/master/javascript/algo.js",
          "rawcode": "console.log('this is a test')",
          "container": {
            "image": "node",
            "tag": "10",
            "entrypoint": "node $ALGO"
          }
        },
        "output": {
          "nodeUri": "https://nile.dev-ocean.com",
          "brizoUrl": "https://brizo.marketplace.dev-ocean.com",
          "brizoAddress": "0x4aaab179035dc57b35e2ce066919048686f82972",
          "metadata": {
            "name": "Workflow output"
          },
          "metadataUrl": "https://aquarius.marketplace.dev-ocean.com",
          "secretStoreUrl": "https://secret-store.nile.dev-ocean.com",
          "whitelist": [
                        "0x00Bd138aBD70e2F00903268F3Db08f2D25677C9e",
                        "0xACBd138aBD70e2F00903268F3Db08f2D25677C9e"
           ],
           "owner":"0x200",
           "publishoutput":true,
           "publishalgolog":true
        }
      }
    ]
  }
}
```

Returns:
A string containing jobId




## Status and Result
  
  
### GET /api/v1/compute
   
   
Get all jobs and corresponding stats

Parameters
```
        signature: String object containg user signature (signed message)
        serviceAgreementId: String object containing agreementID (optional)
        jobId: String object containing workflowID (optional)
        
```

Returns

An Array of objects, each object describing a workflow. If the array is empty, then the search yeld no results

Each object will contain:
```
        owner:The owner of this compute job
        agreementId:
        jobId:
        dateCreated:Unix timestamp of job creation
        dateFinished:Unix timestamp when job finished
        status:  Int, see below for list
        statusText: String, see below
        configlogURL: URL to get the configuration log (for admins only)
        publishlogURL: URL to get the publish log (for admins only)
        algoLogURL: URL to get the algo log (for user)
        outputsURL: Array of URLs for algo outputs
```

Status description:

| status   | Description        |
|----------|--------------------|
|  10       | Job started        |
|  20       | Configuring volumes|
|  30       | Provisioning success |
|  31       | Data provisioning failed |
|  32       | Algorithm provisioning failed |
|  40       | Running algorith   |
|  50       | Filtering results  |
|  60       | Publishing results |
|  70       | Job completed      |


Example:
```
GET /api/v1/compute?signature=0x00110011&serviceAgreementId=0x1111&jobId=012023
```

Output:
```
[
      {
        "owner":"0x1111",
        "agreementId":"0x2222",
        "jobId":"3333",
        "dateCreated":"2020-10-01T01:00:00Z",
        "dateFinished":"2020-10-01T01:00:00Z",
        "status":5,
        "statusText":"Job finished",
        "configlogURL":"http://example.net/logs/config.log",
        "publishlogURL":"http://example.net/logs/publish.log",
        "algoLogURL":"http://example.net/logs/algo.log",
        "outputsURL":[
            {
            "http://example.net/logs/output/0",
            "http://example.net/logs/output/1"
            }
         ]
       },
       {
        "owner":"0x1111",
        "agreementId":"0x2222",
        "jobId":"3333",
        "dateCreated":"2020-10-01T01:00:00Z",
        "dateFinished":"2020-10-01T01:00:00Z",
        "status":5,
        "statusText":"Job finished",
        "configlogURL":"http://example.net/logs2/config.log",
        "publishlogURL":"http://example.net/logs2/cpublish.log",
        "algoLogURL":"http://example.net/logs2/algo.log",
        "outputsURL":[
            {
            "http://example.net/logs2/output/0",
            "http://example.net/logs2/output/1"
            }
         ]
       }
 ]
 ```
       
## Stop
  
  
### PUT /api/v1/compute
   
   
Stop a running compute job.

Parameters
```
        signature: String object containg user signature (signed message)
        serviceAgreementId: String object containing agreementID (optional)
        jobId: String object containing workflowID (optional)
        
        At least one parameter is required (can be any of them)
```

Returns

Status, whether or not the job was stopped successfully.

Example:
```
PUT /api/v1/compute?signature=0x00110011&serviceAgreementId=0x1111&jobId=012023
```

Output:
```
OK
```

## Delete

### DELETE /api/v1/compute

Delete a compute job and all resources associated with the job. If job is running it will be stopped first.

Parameters
```
        signature: String object containg user signature (signed message)
        serviceAgreementId: String object containing agreementID (optional)
        jobId: String object containing workflowID (optional)
        
```

Returns

Status, whether or not the job was removed successfully.

Example:
```
DELETE /api/v1/compute?signature=0x00110011&serviceAgreementId=0x1111&jobId=012023
```

Output:
```
OK
```