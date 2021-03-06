# Java Cloud Function for HL7 file parsing and data type extraction: hl7-parser-cloud-function

This directory contains a simple IBM Cloud Function in Java, HL7Parser, that uses the HAPI library to handle HL7 file format of whatever trigger event type. It validates, using a default validation rules set the file format, the HL7 input and extracts some data type belonging to different file segment. In addition there are two more classes, HL7Validation and MainHL7Validation, implementing a main class that instantiates and calls the other one, that uses a little bit more specific HAPI library classes related to the ORU_R01 HL7 trigger event. 

HL7 or Health Level-7 refers to a set of international standards for transfer of clinical and administrative data between software applications used by various healthcare providers. Hospitals and other healthcare provider organizations typically have many different computer systems used for everything from billing records to patient tracking. All of these systems should communicate with each other. HL7 International specifies a number of flexible standards, guidelines, and methodologies by which various healthcare systems can communicate with each other. These data standards are meant to allow healthcare organizations to easily share clinical information.

The HL7 ORU-R01 message transmits observations and results from the producing system/filler to the ordering system/placer. It may also be used to transmit result data from the producing system to a medical record archival system, or to another system not part of the original order process. ORU messages are also sometimes used to register or link to clinical trials, or for medical reporting purposes for drugs and devices.

Refer to IBM Cloud Functions documentation for details at this link: [creating-java-actions](https://console.bluemix.net/docs/openwhisk/openwhisk_actions.html#creating-java-actions).


## Java class input parameter:
 * `JsonObject` The input JSON Object required by IBM Cloud Specifications about Java Cloud Functions and actions. 
    Sample:
 
 ```
 {
  "hl7" : "MSH|^~\\&|SQLAB||SMS||201601081056||ORU^R01|34|P|2.2|151760005618||||||^^^^^
  PID|000|111|8505059^^^AMC^MR|CCC|ICDTENPONE^TWENTY^^^^^|DDD|19810519|M|EEE|FFF|GGG|HHH|||MMM|NNN|OOO|500084652^^^^^
  ORC|RE|SO80773465-0|||||^^^201601081054|||||08443^PAUZE, DANIEL K|||||||
  OBR||SO80773465-0||LABTYSCR^TYPE + SCREEN (CONVERT)^L^TYSCR^ - ^LN|||201601081054|||||||201601081054|^|08443^PAUZE, DANIEL K||||F16050||||BB|F|TYSCR^TYSCR|^^^^^R|^~^~||||||| - ||||||||  
  OBX|0|ST|%EXX^CROSSMATCH EXPIRATION^L^%EXX^^LN||01/12/2016||||||F|||||^^^^^^^^^^^|"
 }
 ```
 * \n is not supported as HL7 segments separator. We use \r instead. 

## Java class output parameter:
* `JsonObject` The output JSOn Object required by IBM Cloud Specifications about Java Cloud Functions and actions.
Sample in case of a hl7 message parsed as valid of ORU_R01 trigger event type. Data type are extracted from the MSH (messagetype), PID (patientid), ORC (uniqueplacerid), OBR (universalserviceid) and OBX (observationidentifier) segments.

```
  {
    "valid":"true",
    "metadata":
    {
  		"messagetype":"ORU_R01",
  		"patientid":"8505059",
  		"uniqueplacerid":"SO80773465-0",
  		"universalserviceid":"LABTYSCR_TYPE + SCREEN (CONVERT)",
  		"observationidentifier":"%EXX_CROSSMATCH EXPIRATION"
    }
  }
```
* `JsonObject` sample in case of a hl7 message parsed as invalid:

```
  {
     "error":"HL7Exception message"
  }
```
* `JsonObject` sample in case of an empty hl7 message:

```
  {
     "error":"The input json object is null"
  }
```

## Register the JAVA cloud function called hl7parser inside your IBM Cloud org and space using the IBM Cloud CLI

### Prerequisites
1. [Create an account with IBM Cloud](https://console.bluemix.net/registration/)

2. [Install IBM Cloud CLI](https://console.bluemix.net/docs/cli/reference/bluemix_cli/get_started.html#getting-started)

3. [Install IBM Cloud functions CLI plug-in](https://console.bluemix.net/docs/openwhisk/bluemix_cli.html#cloudfunctions_cli)

### Steps 

### 1. Clone the repo

1. Clone the repo locally. In a terminal, run:

```
$ git clone https://github.com/IBM/hl7-parser-cloud-function.git
```
2. Build the java project as a Runnable Jar called hl7parser.jar

3. Log into your IBM Cloud account

```
$ bx login 
```

If you have a federated ID, use bx login --sso to log in to the IBM Cloud CLI.

4. Run command:

```
$  ic wsk action create hl7-parser-cloud-function hl7parser.jar --main com.hl7.parser.hl7_ingestion_parser.HL7Parser
```
* Now you can see and invoke the action from the IBM Cloud UI.
* Java actions cannot be created with the IBM Cloud UI.

 
 

## License

[Apache 2.0](LICENSE)
