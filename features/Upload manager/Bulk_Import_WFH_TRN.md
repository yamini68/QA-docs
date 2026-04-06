```json
{
  "Mode": "Info",
  "Tenant": "KOZ5S",
  "User": "1747117182933",
  "File": "1775477429425",
  "Range": "All"
},

{
    "Data": {
        "OriginalFileName": "Import_Work_From_Home_Template.csv",
        "OriginalFileType": "S",
        "OriginalFileExtension": "csv",
        "AddedOnDate": 1775477429425
    }
}
```


```json
{
  "function": "getData",
  "expression": "[KOZ5S^TRF^00015^1706865256121^A^*^*^* ],[],[Header^Fields^Applicability^Workflow^FieldAttributes^Entities^TransactionInitiation^TransactionCancellation^Conversations^Conversation^ExceptionRules^ExceptionValidation^DataTags^DynamicDocuments^DynamicDocument],[0^50]"
}

```

```json
{
  "RequestType": "C",
  "DocumentType": "TRN",
  "TenantID": "KOZ5S",
  "FeatureID": "00015",
  "FeatureVariantID": 1706865256121,
  "FeatureVariantName": "Initiate Data Import Settings",
  "StepID": "000",
  "ActionID": "IN",
  "SubjectUser": null,
  "MenuOptionID": "00015",
  "TransactionUser": 1747117182933,
  "Payload": {
    "Fields": {
      "UploadTemplate": 1774077925781,
      "InputFile": 1775477429425,
      "TransactionsFile": 1775477429702
    },
    "Entities": []
  },
  "EffectiveTill": null
},


{
    "FeatureID": "00015",
    "FeatureVariantID": 1706865256121,
    "Message": "Transaction Document Inserted",
    "DocumentID": "KOZ5S:TRN:00015:1706865256121:1775477460964:001",
    "DocumentType": "TRN",
    "TransactionID": 1775477460964
}
```
