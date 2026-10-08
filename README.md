## Felrapport. 

1. **Krasch vid start**

***Vad hände:*** 
Programmet kraschar direkt med `IndexOutOfRange` i `Load()`. 

***Varför:*** 
Filen slutar med en radbrytning, så `Split('\n')` ger en tom sista rad. Den tomma raden har inget `;`, så `parts` får bara ett element och `parts[1]` finns inte.

***Lösning:***
Det behövs en `if` som kollar `parts.Length < 2`. En giltig rad har två delar (pris och namn). Har raden färre delar hoppar `continue` över resten av varvet och går vidare till nästa rad.

Efter fel 2 läser jag filen med `File.ReadAllLines`, och då blir det ingen tom sista rad längre. `if`-satsen ligger ändå kvar, eftersom den skyddar mot tomma eller trasiga rader om någon ändrar `items.txt` för hand.
<br>
<br>

- 1.1. **Följdfel: Varunamnen syns inte**

***Vad hände:*** 
Samma orsak som fel 1, alltså `Split('\n')`, gav också ett andra problem: Programmet startar men produkterna skrivs inte ut, endast priset.

***Varför:*** 
För att `\r` flyttar markören till början av raden och skriver över `1. Mjölk` med `- 15 kr`. `Save()` skriver `\r\n` efter varje rad, men `Load()` klippte bara på `\n`, så `\r` blev kvar i slutet av varje namn. Därför hittade inte sökningen heller varorna.

***Lösning:*** 
Jag bytte ut

```csharp
string text = File.ReadAllText(path);
string[] lines = text.Split('\n');
```

mot `File.ReadAllLines(path)`, som klarar både `\n` och `\r\n`, så inget `\r` blir kvar.
<br>
<br>

2. **Fel totalsumma**

***Vad hände:*** 
Totalsumman stämmer inte. Den visar 121 kr isället för 136 kr (15+32+89), mjölken räknades inte med.

***Varför:*** 
Loopen i `Total()` hoppar över första varan. Listans första element har index 0, men loopen startade på `i = 1`, så `items[0]` kom aldrig med i summan. 

***Lösning:*** 
Lösningen är att byta ut `i = 1` till `i = 0`
<br>
<br>

3. **Krasch vid bokstäver istället för tal**

***Vad hände:*** 
Om man skriver bokstäver där programmet vill ha ett tal (i menyn, vid priset eller vid numret) kraschar programmet med `FormatException`.

***Varför:*** 
`int.Parse` försöker göra om texten till ett tal. Går det inte så kastar den ett undantag, men eftersom ingen fångar upp det så kraschar programmet.

***Lösning:*** 
Jag bytte ut `int.Parse` mot `int.TryParse` på alla tre ställena. `TryParse` kraschar inte, utan returnerar `false` om texten inte är ett tal. Då skriver programmet ut ett felmeddelande, och `continue` visar menyn igen.  
<br>

4. **Kraschar när man försöker ta bort en vara som inte finns**

***Vad hände:***
Om man valde "Ta bort vara" och valde ett nummer som inte fanns i listan så kraschade programmet med `ArgumentOutOfRangeException`.

**Varför:**
`RemoveAt` gjorde om numret till ett index (`number - 1`) och skickade det diretkt till `items.RemoveAt`, utan att kontrollera om det fanns. Med tre varor är bara index 0-2 giltiga.

**Lösning:** 
`RemoveAt` kontrollerar nu att numret ligger mellan 1 och antalet varor. Om inte så returnerar den `false` istället för att krascha, och `true` om varan togs bort. `Program.cs` kollar svaret och skriver ut ett meddelande om numret inte fanns. Då svarar `ShoppingList` bara på om det gick, och `Program.cs` sköter kontakten med användaren. 


5. Sjätte
6. 

## Designval. 
* Hur Add säger nej när taket överskrids, och varför du valde så.

## Klassdiagram. 
Ett enkelt diagram över programmet efter dina ändringar. Tre rutor räcker.




