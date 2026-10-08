## Felrapport. 

1. **Krasch vid start**

**Vad hände:**
Programmet kraschar direkt med `IndexOutOfRangeException` i `Load()`. 

**Varför:**
Filen slutar med en radbrytning, så `Split('\n')` ger en tom sista rad. Den tomma raden har inget `;`, så `parts` får bara ett element och `parts[1]` finns inte.

**Lösning:**
Det behövs en `if` som kollar `parts.Length < 2`. En giltig rad har två delar (pris och namn). Har raden färre delar hoppar `continue` över resten av varvet och går vidare till nästa rad.

Efter fel 1.1 läser jag filen med `File.ReadAllLines`, och då blir det ingen tom sista rad längre. `if`-satsen ligger ändå kvar, eftersom den skyddar mot tomma eller trasiga rader om någon ändrar `items.txt` för hand.

- 1.1. **Följdfel: Varunamnen syns inte**

**Vad hände:**
Samma orsak som fel 1, alltså `Split('\n')`, gav också ett andra problem: Programmet startar men produkterna skrivs inte ut, endast priset.

**Varför:**
För att `\r` flyttar markören till början av raden och skriver över `1. Mjölk` med `- 15 kr`. `Save()` skriver `\r\n` efter varje rad, men `Load()` klippte bara på `\n`, så `\r` blev kvar i slutet av varje namn. Därför hittade inte sökningen heller varorna.

**Lösning:**
Jag bytte ut

```csharp
string text = File.ReadAllText(path);
string[] lines = text.Split('\n');
```

mot `File.ReadAllLines(path)`, som klarar både `\n` och `\r\n`, så inget `\r` blir kvar.

2. **Fel totalsumma**

**Vad hände:**
Totalsumman stämmer inte. Den visar 121 kr i sället för 136 kr (15+32+89), mjölken räknades inte med.

**Varför:** 
Loopen i `Total()` hoppar över första varan. Listans första element har index 0, men loopen startade på `i = 1`, så `items[0]` kom aldrig med i summan. 

**Lösning:** 
Lösningen är att byta ut `i = 1` till `i = 0`.

3. **Krasch vid bokstäver istället för tal**

**Vad hände:** 
Om man skriver bokstäver där programmet vill ha ett tal (i menyn, vid priset eller vid numret) kraschar programmet med `FormatException`.

**Varför:** 
`int.Parse` försöker göra om texten till ett tal. Går det inte så kastar den ett undantag, men eftersom ingen fångar upp det så kraschar programmet.

**Lösning:** 
Jag bytte ut `int.Parse` mot `int.TryParse` på de tre ställena i Program.cs. `TryParse` kraschar inte, utan returnerar `false` om texten inte är ett tal. Då skriver programmet ut ett felmeddelande, och `continue` visar menyn igen.  
Samma sak gällde `int.Parse` i `Load()`. En rad i filen som `abc;Ost` hade kraschat programmet vid start. Där använder jag också `int.TryParse`, och en rad med ogiltigt pris hoppas över med `continue`.

4. **Kraschar när man försöker ta bort en vara som inte finns**

**Vad hände:**
Om man valde "Ta bort vara" och valde ett nummer som inte fanns i listan så kraschade programmet med `ArgumentOutOfRangeException`.

**Varför:**
`RemoveAt` gjorde om numret till ett index (`number - 1`) och skickade det direkt till `items.RemoveAt`, utan att kontrollera om det fanns. Med tre varor är bara index 0-2 giltiga.

**Lösning:** 
`RemoveAt` kontrollerar nu att numret ligger mellan 1 och antalet varor. Om inte så returnerar den `false` istället för att krascha, och `true` om varan togs bort. `Program.cs` kollar svaret och skriver ut ett meddelande om numret inte fanns. Då svarar `ShoppingList` bara på om det gick, och `Program.cs` sköter kontakten med användaren. 

5. **Kraschar när items.txt saknas**

**Vad hände:**
Om `items.txt` inte fanns så kraschade programmet direkt vid start med `FileNotFoundException`.

**Varför:** 
`File.ReadAllLines` antar att filen finns. Inget fångade undantaget, så programmet kraschade. Det händer innan någon vara har sparats och programmet körs första gången.

**Lösning:** Jag valde att lägga `File.ReadAllLines` i en `try` och fångar `FileNotFoundException` i en `catch`. Då skrivs ett meddelande ut och `Load()` avslutas med `return;`, så programmet startar med en tom lista. Variabeln `lines` deklareras före `try`, eftersom den annars bara finns inuti `try`-blocket.

6. **Fel vid sparande doldes**

**Vad hände:**
Även om sparandet misslyckades, t.ex. för att filen var skrivskyddad, så skrev programmet ut "Listan är sparad". Användaren trodde att listan är sparad, men varorna försvann vid omstart.

**Varför:** 
`catch` i `Save()` var tom, så felet fångades och kastades bort utan att någon fick veta det. Dessutom låg "Listan är sparad." efter `try`/`catch` så den skrevs ut oavsett om det sparades eller inte. 

**Lösning:**
Jag flyttade "Listan är sparad." in i `try`, direkt efter `File.WriteAllText`. Den körs nu bara om sparandet lyckades. Den tomma `catch` ersattes med två specifika: `UnauthorizedAccessException` (saknar behörighet) och `IOException` (andra fel vid skrivning). Båda skriver ut ett meddelande om vad som gick fel.

## Designval. 
* Hur Add säger nej när taket överskrids, och varför du valde så.

## Klassdiagram. 
Ett enkelt diagram över programmet efter dina ändringar. Tre rutor räcker.




