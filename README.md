## Felrapport. 

1. **Krasch vid start**

***Vad hände:*** 
Programmet kraschar direkt med `IndexOutOfRange` i `Load()`. 

***Varför:*** 
Filen slutar med en radbrytning, så `Split('\n')` ger en tom sista rad. Den tomma raden har inget `;`, så `parts` får bara ett element och `parts[1]` finns inte.

***Lösning:***
Det behövs en `if` som kollar `parts.Length < 2`. En giltig rad har två delar (pris och namn). Har raden färre delar hoppar `continue` över resten av varvet och går vidare till nästa rad.

Efter fel 2 läser jag filen med `File.ReadAllLines`, och då blir det ingen tom sista rad längre. `if`-satsen ligger ändå kvar, eftersom den skyddar mot tomma eller trasiga rader om någon ändrar `items.txt` för hand.

2. **Varunamnen syns inte**

***Vad hände:*** 
Programmet startar, men produkterna skrivs inte ut, endast priset.

***Varför:*** 
För att `\r` flyttar markören till början av raden och skriver över `1. Mjölk` med `- 15 kr`. `Save()` skriver `\r\n` efter varje rad, men `Load()` klippte bara på `\n`, så `\r` blev kvar i slutet av varje namn. Därför hittade inte sökningen heller varorna.

***Lösning:*** 
Jag bytte ut

```csharp
string text = File.ReadAllText(path);
string[] lines = text.Split('\n');
```

mot `File.ReadAllLines(path)`, som klarar både `\n` och `\r\n`, så inget `\r` blir kvar.

3. **Fel totalsumma**

***Vad hände:*** 
Totalsumman stämmer inte. Den visar 121 kr isället för 136 kr (15+32+89), mjölken räknades inte med.

***Varför:*** 
Loopen i `Total()` hoppar över första varan. Listans första element har index 0, men loopen startade på `i = 1`, så `items[0]` kom aldrig med i summan. 

***Lösning:*** 
Lösningen är att byta ut `i = 1` till `i = 0`

4. **Krasch vid bokstäver istället för tal**

***Vad hände:*** 
Om man skriver bokstäver där programmet vill ha ett tal (i menyn, vid priset eller vid numret) kraschar programmet med `FormatException`.

***Varför:*** 
`int.Parse` försöker göra om texten till ett tal. Går det inte så kastar den ett undantag, men eftersom ingen fångar upp det så kraschar programmet.

***Lösning:*** 
Jag bytte ut `int.Parse` mot `int.TryParse` på alla tre ställena. `TryParse` kraschar inte, utan returnerar `false` om texten inte är ett tal. Då skriver programmet ut ett felmeddelande, och `continue` visar menyn igen.  


5. Femte
6. Sjätte

## Designval. 
* Hur Add säger nej när taket överskrids, och varför du valde så.

## Klassdiagram. 
Ett enkelt diagram över programmet efter dina ändringar. Tre rutor räcker.




