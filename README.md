# please re-check all api url
* you can now use = https://test.stayexponential.com/
* /user/api/ => /api/v1/user/
* /api-token-auth/ => /api-token-auth/
* /user/api/change-password/ => /api/v1/change-password/
* ...
* please re-check all you api url
* "api-version" in json is no needed


# vpms api
- in this version of api, results are in these two codes

```
201 success
400 fail
```

## create a user
POST /api/v1/user/
```json
{
    "act": "create",
    "detail": {
        "username": "testuser",
        "password": "testpassword"
    }
}
```


## auth token
POST /api-token-auth/
```json
{
    "username":"test",
    "password":"test1234"
}
```
result
```json
{"token":"ad7a639d38102bb77f8da52d1d7a4f0987e51fc7"}
```
### you need to include this token in header like this
```json
{
    "Authorization":"Token ad7a639d38102bb77f8da52d1d7a4f0987e51fc7"
}
```

## change user password
POST /api/v1/change-password/
```json
{
    "act":"change password",
    "detail":{
        "new password":"pa55w0rd"
    }
}
```


## create invitation url
POST /api/v1/give-permission/
```json
{
    "act": "create",
    "detail": [
        {
            "level": "200"
        },
        {
            "level": "300"
        }
    ]
}
```
* danger! a level that a user create should be lower than his/her profile level
* will be updated in the next version
```
# Level 200 - System Admin //Who able to creat new chain and see other chain.
# Level 300 - Chain Admin //Who able to create configuration and property with update, And unable to see other chain.
# Level 400 - Property Admin //Who able to create and configuration and update property configuration. with single and multiple property depend on assigned.
# Level 500 - Property Manager
# Level 600 - Operation Manager
# Level 700 - Duty manager/ Supervisor
# Level 800 - Officer
#######################
# Level 10000 - T   `q567890-bgnhyop[c vbnemplate
# Level 10001 - blah blah blah blah blah blah blah blah blah blah blah blah blah blah blah blah blah
```


## get invitation url
GET /api/v1/give-permission/

## update invitation url status
* status can be "0" or "1"
```json
{
    "act": "update",
    "detail": {
        "url": "xjT2pyN5dK5LaBB",
        "status": "1"
    }
}
```

## ex. for upload an image or file
```dart
//https://stackoverflow.com/questions/56252856/how-to-pick-files-and-images-for-upload-with-flutter-web


import 'package:http/http.dart' as http;
import 'package:file_picker/file_picker.dart';
import 'package:flutter/material.dart';

class FileUploadWithHttp extends StatefulWidget {
  @override
  _FileUploadWithHttpState createState() => _FileUploadWithHttpState();
}

class _FileUploadWithHttpState extends State<FileUploadWithHttp> {
  PlatformFile objFile = null;

  void chooseFileUsingFilePicker() async {
    //-----pick file by file picker,

    var result = await FilePicker.platform.pickFiles(
      withReadStream:
          true, // this will return PlatformFile object with read stream
    );
    if (result != null) {
      setState(() {
        objFile = result.files.single;
      });
    }
  }

  void uploadSelectedFile() async {
    //---Create http package multipart request object
    final request = http.MultipartRequest(
      "POST",
      Uri.parse("Your API URL"),
    );
    //-----add other fields if needed
    request.fields["id"] = "abc";

    //-----add selected file with request
    request.files.add(new http.MultipartFile(
        "Your parameter name on server side", objFile.readStream, objFile.size,
        filename: objFile.name));

    //-------Send request
    var resp = await request.send();

    //------Read response
    String result = await resp.stream.bytesToString();

    //-------Your response
    print(result);
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Column(
        children: [
          //------Button to choose file using file picker plugin
          RaisedButton(
              child: Text("Choose File"),
              onPressed: () => chooseFileUsingFilePicker()),
          //------Show file name when file is selected
          if (objFile != null) Text("File name : ${objFile.name}"),
          //------Show file size when file is selected
          if (objFile != null) Text("File size : ${objFile.size} bytes"),
          //------Show upload utton when file is selected
          RaisedButton(
              child: Text("Upload"), onPressed: () => uploadSelectedFile()),
        ],
      ),
    );
  }
}
```
## create user profile
* you can get invitation token from invitation url
* stayexponential.com/create-profile/invitation-token/
* "test-token-from-admin" can be use for testing 

* one user == one profile
* if you want to edit the existing profile, you need to use update action

POST /api/v1/user-profile/
```json
{
    "act": "create",
    "invitation": "test-token-from-admin",
    "detail": {
        "first_name":"testcreateprofile",
        "middle_name":"middlename",
        "last_name":"testlastname",
        "gender":"male",
        "date_of_birth": "2021-12-25",
        "phone_number": "+66789878878",
        "department": "hilton"
    }
}
```
* all invitation urls will expire after 24 hours.
* in the future the user's photo should be upload here
* will be updated in the next version

## get user profile
GET /api/v1/user-profile/

## update user profile
POST /api/v1/user-profile/
```json
{
    "act": "update",
    "invitation": "test-token-from-admin",
    "detail": {
        "first_name":"updated-first-name",
        "middle_name":"middlename",
        "last_name":"new-last-name",
        "gender":"male",
        "date_of_birth": "2021-12-25",
        "phone_number": "+66789878878",
        "department": "hilton"
    }
}
```

# Configuration
## create chain
POST /api/v1/chain/
```json
{
    "act": "create",
    "detail": {
        "code": "hiltone",
        "description": "descriptionnnn"
    }
}
```
* this feature allow for user.level less than 300 
* one user => one chain
* chain code will be changed to "hiltone" => "HILTONE"
* it is a good idea to validate the upper case before connect to this api
* chain code must be less than or equal to 8 characters

## get chain 
GET /api/v1/chain/

## update chain
POST /api/v1/chain/
```json
{
    "act": "update",
    "detail": {
        "code": "newhilto",
        "description": "descriptionnnn"
    }
}
```

## create property 
POST /api/v1/chain-property/
```json
{
    "act": "create",
    "detail": {
        "name": "hilton-pattaya",
        "code": "HILPAT",
        "number_of_room": 30,
        "property_address": "somethinogggoeucoeguc",
        "city": "thoteu",
        "state_province": "province",
        "country_description": "charfieldddd",
        "postal_code": "charfielddddd",
        "hotel_date": "2021-11-22",
        "accounting_date": "2021-11-23",
        "telephone": "charfieldddd",
        "fax": "charfieldddd",
        "email": "charfieldddd",
        "website": "charfieldddd",
        "tax_percentage": "25",
        "hotel_tax_id": "charfieldddd",
        "hotel_tax_name": "charfieldddd",
        "hotel_branch_id": "charfieldddd",
        "hotel_branch_name": "charfieldddd"
    }
}
```
* integer field could be 30 or "30"
* code.length must be less than or equal to 8 characters

## get property
### get all properties
- GET /api/v1/chain-property/
### get specific properties
- GET /api/v1/chain-property/?code=HILPEE
- GET /api/v1/chain-property/?code=HILPEE&city=test

## update property
POST /api/v1/chain-property/
```json
{
    "act": "update",
    "old-code": "HILTON",
    "detail": {
        "hotel_date": "2021-11-22",
        "telephone": "0899998898988",
        "website": "new web",
    }
}
```
```json
{
    "act": "update",
    "old-code": "HILTON",
    "detail": {
        "website": "new web",
    }
}
```

## get bed-type, exposure, zone, attribute
- GET /api/v1/bed-type/
- GET /api/v1/exposure/
- GET /api/v1/zone/
- GET /api/v1/attribute/

## create bed-type, exposure, zone, attribute
- POST /api/v1/bed-type/
- POST /api/v1/exposure/
- POST /api/v1/zone/
- POST /api/v1/attribute/

```json
{
    "act":"create",
    "detail":{
        "code":"newobj",
        "description":"charfielddd255"
    }
}
```

## update bed-type, exposure, zone, attribute
- POST /api/v1/bed-type/
- POST /api/v1/exposure/
- POST /api/v1/zone/
- POST /api/v1/attribute/

```json
{
    "act": "update",
    "old-code": "single-bed",
    "detail": {
        "code": "double",
        "description": "char255"
    }
}
```


## get building
- GET /api/v1/building/

## create building
- POST /api/v1/building/
```json
{
    "act": "create",
    "detail": {
        "code": "bu01",
        "name": "carbin",
        "description": "dessdesdeschar255"
    }
}
```

## update building
- POST /api/v1/building/
```json
{
    "act": "update",
    "old-code": "bu01",
    "detail": {
        "code": "bu01-1",
        "description": "desdeschar255"
    }
}
```


## get floor
### get all floor
- GET /api/v1/floor/
### get floor in a building
- GET /api/v1/floor/?building=b1

## create floor
- POST /api/v1/floor/
```json
{
    "act": "create",
    "building-code": "b1",
    "detail": {
        "code": "fl1",
        "name": "namena",
        "description": "dessdesdes255"
    }
}
```

## update floor
- POST /api/v1/floor/
```json
{
    "act": "update",
    "building-code": "b1",
    "old-code": "fl2",
    "detail": {
        "code": "fl2-1",
        "name": "floorname",
        "description": "dessdesdes255"
    }
}
```

## get room
### get all room
- GET /api/v1/room/

### get room in a building
- GET /api/v1/room/?building=b1

### get room in a floor
- GET /api/v1/room/?building=b1&floor=fl01

## create room

## update room
