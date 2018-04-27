---
title: "StayInSync Property Example (VB)"
 
 
manager: soliver
ms.date: 11/16/2014
ms.audience: Developer
ms.topic: reference
  
localization_priority: Normal
ms.assetid: 1b35f19a-0104-efd5-5222-55f92e08473b
description: "This example demonstrates how the StayInSync property facilitates accessing rows in a hierarchical Recordset."
---

# StayInSync Property Example (VB)

This example demonstrates how the [StayInSync](stayinsync-property-ado.md) property facilitates accessing rows in a hierarchical [Recordset](recordset-object-ado.md).
  
The outer loop displays each author's first and last name, state, and identification. The appended **Recordset** for each row is retrieved from the [Fields](fields-collection-ado.md) collection and automatically assigned to **rstTitleAuthor** by the **StayInSync** property whenever the parent **Recordset** moves to a new row. The inner loop displays four fields from each row in the appended recordset. 
  
```
 
'BeginStayInSyncVB 
Public Sub Main() 
 On Error GoTo ErrorHandler 
 
 'To integrate this code create a DSN called Pubs 
 'with a user_id = sa and no password 
 
 Dim Cnxn As ADODB.Connection 
 Dim rst As ADODB.Recordset 
 Dim rstTitleAuthor As New ADODB.Recordset 
 Dim strCnxn As String 
 
 ' open connection with Data Shape attributes 
 Set Cnxn = New ADODB.Connection 
 strCnxn = "Provider=MSDataShape;Data Provider='sqloledb';Data Source='MySqlServer';" &amp; _ 
 "Initial Catalog='Pubs';Integrated Security='SSPI';" 
 Cnxn.Open strCnxn 
 
 ' create recordset 
 Set rst = New ADODB.Recordset 
 rst.StayInSync = True 
 rst.Open "SHAPE {select * from Authors} " &amp; _ 
 "APPEND ( { select * from titleauthor} AS chapTitleAuthor " &amp; _ 
 "RELATE au_id TO au_id) ", Cnxn, , , adCmdText 
 
 Set rstTitleAuthor = rst("chapTitleAuthor").Value 
 Do Until rst.EOF 
 Debug.Print rst!au_fname &amp; " " &amp; rst!au_lname &amp; " " &amp; _ 
 rst!State &amp; " " &amp; rst!au_id 
 
 Do Until rstTitleAuthor.EOF 
 Debug.Print rstTitleAuthor(0) &amp; " " &amp; rstTitleAuthor(1) &amp; " " &amp; _ 
 rstTitleAuthor(2) &amp; " " &amp; rstTitleAuthor(3) 
 rstTitleAuthor.MoveNext 
 Loop 
 
 rst.MoveNext 
 Loop 
 
 ' clean up 
 rst.Close 
 Cnxn.Close 
 Set rst = Nothing 
 Set Cnxn = Nothing 
 Exit Sub 
 
ErrorHandler: 
 ' clean up 
 If Not rst Is Nothing Then 
 If rst.State = adStateOpen Then rst.Close 
 End If 
 Set rst = Nothing 
 
 If Not Cnxn Is Nothing Then 
 If Cnxn.State = adStateOpen Then Cnxn.Close 
 End If 
 Set Cnxn = Nothing 
 
 If Err <> 0 Then 
 MsgBox Err.Source &amp; "-->" &amp; Err.Description, , "Error" 
 End If 
End Sub 
'EndStayInSyncVB 

```

