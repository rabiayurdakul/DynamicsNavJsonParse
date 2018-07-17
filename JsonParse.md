
```pascal
//If you have a single record json parse
// DataCount int
// RecCount int
// DummyText text
// JsonData text
// RecObject text
// Pos1 int
// Pos2 int
// Lenght int
// DataHeader text
// DataText text

//Page objects:
// CustomerID(text), TCIdentityNo(text), CustNameLastName(text), MobilePhoneNo(text), EmailAddress(text), 
// Birthday(date), WeddingAnn(date), Profession(text), SpecialNote(text) 

// Example JsonData 
// [{"MusteriID":"125896","TcNo":"1111111111","MusteriAdiSoyadi":"Rabia Yurdakul","CepTel":"23651256","Email":"rfdf@gghg.com"}]

// If multiple rows of data are coming up, you can create a buffer table and save the same data to the buffer table.
```

```pascal
DataCount := 0;
RecCount :=1;

DummyText := JsonData;

IF STRLEN(DummyText) > 1 THEN BEGIN
  REPEAT
    RecObject := COPYSTR(DummyText,1,STRPOS(DummyText,','));
    DummyText := COPYSTR(DummyText,STRPOS(DummyText,',')+1);
    RecCount +=1; 
  UNTIL STRPOS(DummyText,',') =0;
END;

IF STRLEN(JsonData) > 0 THEN BEGIN
  REPEAT
    DataCount +=1;

    IF DataCount = 1 THEN
      Pos1 := STRPOS(JsonData,'{') +1
    ELSE
      Pos1 := 1;

    IF DataCount <> RecCount THEN
      Pos2 := STRPOS(JsonData, ',')
    ELSE
      Pos2 := STRPOS(JsonData,'}');

    IF Pos2 <> 0 THEN
      Lenght := Pos2 - Pos1
    ELSE BEGIN
      Lenght := 1;
      Pos1 :=1;
      Pos2 := 1;
      END;

    CurrentObject := COPYSTR(JsonData,Pos1,Lenght);
    JsonData := COPYSTR(JsonData,Pos2+1);
    Pos2 := STRPOS(CurrentObject,':')-1;
    Lenght := Pos2-2;
    DataHeader := COPYSTR(CurrentObject,2,Lenght);
    CurrentObject := COPYSTR(CurrentObject,Pos2+2);
    DataText := COPYSTR(CurrentObject,2,STRLEN(CurrentObject)-2);
    CASE DataHeader OF
      'MusteriID' : CustomerID := DataText;
      'TcNo' : TCIdentityNo := DataText;
      'MusteriAdiSoyadi' : CustNameLastName := DataText;
      'CepTel' : MobilePhoneNo := DataText;
      'Email' : EmailAddress := DataText;
      'Birthday' : EVALUATE(Birthday,DataText);
      'WeddingAnn' : EVALUATE(WeddingAnn,DataText);
      'Profession' : Profession := DataText;
      'SpecialNote' : SpecialNote := DataText;
      END;
     UNTIL DataCount = RecCount;

END;

```
