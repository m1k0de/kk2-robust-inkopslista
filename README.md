## Felrapport. 

De **sex felen**: 
vad hände, varför och hur du löste det. Några rader per fel räcker.

1. 
***Vad hände:*** Programmet kraschar direkt med `IndexOutOfRange` i `Load()`. 

***Varför:*** Filen slutar med en radbrytning, så `Split('\n')` ger en tom sista rad. Den tomma raden har inget `;`, så `parts` får bara ett element och `parts[1]` finns inte.

***Lösning:***
Det behövs en `if` som kollar `parts.Length < 2`. En giltig rad har två delar (pris och namn). Har raden färre delar hoppar `continue` över resten av varvet och går vidare till nästa rad.

2. 
***Vad hände:*** Programmet startar, men produkterna skrivs inte ut, endast priset.

***Varför:*** För att `\r` flyttar markören till början av raden och skriver över `1. Mjölk` med `- 15 kr`. `Save()` skriver `\r\n` efter varje rad, men `Load()` klippte bara på `\n`, så `\r` blev kvar i slutet av varje namn. Därför hittade inte sökningen heller varorna.

***Lösning:*** Jag bytte ut

```csharp
string text = File.ReadAllText(path);
string[] lines = text.Split('\n');
```

mot `File.ReadAllLines(path)`, som klarar både `\n` och `\r\n`, så inget `\r` blir kvar.

3. Tredje
4. Fjärde
5. Femte
6. Sjätte

## Designval. 
* Hur Add säger nej när taket överskrids, och varför du valde så.

## Klassdiagram. 
Ett enkelt diagram över programmet efter dina ändringar. Tre rutor räcker.




