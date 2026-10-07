## Felrapport. 

De **sex felen**: 
vad hände, varför och hur du löste det. Några rader per fel räcker.

1. 
***Vad hände:*** Programmet crashar direkt med ```IndexOutOfRange``` i ```Load()```. 

***Varför:*** Filen slutar med en radbrytning, så ```Split(\n)```ger en tom sista rad. Den tomma raden har inget ```;```, så ```parts``` får bara ett element och ```parts[1]```finns inte.

***Lösning:***
Det behövs en ```if``` som kollar ```parts.Length < 2```. En giltig rad har två delar (pris och namn). Har raden färre delar hoppar ```continue``` över resten av varvet och går vidare till nästa rad.

2. Andra
3. Tredje
4. Fjärde
5. Femte
6. Sjätte

## Designval. 
* Hur Add säger nej när taket överskrids, och varför du valde så.

## Klassdiagram. 
Ett enkelt diagram över programmet efter dina ändringar. Tre rutor räcker.




