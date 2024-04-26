# Enkelvoudige lineaire regressie (regressie met één predictor)

## data
Je start met $n$ gepaarde waarnemingen van een response variabele $y$ en een predictor variabele $x$.

## berekening in R

Als je op basis van deze waarnemingen de parameters voor de lijn en betrouwbaarheid van die parameters wilt berekenen is de gemakkelijkste manier de volgende code in R (R is een geinterpreteerde programmeertaal, de de-facto standaard voor statistische berekenignen).

```
y <- c(12,11,12,15,13,16,13,18,11,14)
x <- c(50,51,62,45,63,76,53,68,51,74)
model <- lm(y~x)
confint(model, level = .95)
```
**uitleg**: 
- hierin hoeft alleen de getallen binnen  c( ...) te vervangen.
- het commando lm() fit het lineaire model
- het commando confint berekent de betrouwbaarheidsintervallen (in dit geval 95% intervallen) voor de 2 parameters van het lineaire model en print de output

Als je de software R niet hebt geinstalleerd, kun je deze publieke pagina gebruiken: https://colab.research.google.com/drive/1UkqZ0xEM6mkkb1Z2wEZN1ibWyRkXXzIC?usp=sharing

[colab is een soort google-drive/google-docs voor code, in colab notebooks kun je R-code of python code uitvoeren]

## berekening 'met de hand'
Op basis van die waarnemingen bereken je de volgende vergelijking voor een lijn
$$\hat{y} = b_0 + b_1 x$$

waarin $b_0$ de intercept parameter, en $b_1$ de helling parameter (kortweg intercept en helling). $\hat{y}$ is dus de _voorspelde_ response $y$, terwijl $x$ de waargenomen predictor variabele is. 

De helling  $b_1$ wordt berekend door de volgende formule
$$ b_1 =  \frac{\sum_{i=1}^{n}{(x_i - \bar{x})(y_i - \bar{y})}} {(x_i - \bar{x})^2}$$

waar $\bar{x}$ het gemiddelde is van de $x$-waarnemingen en $\bar{y}$ het gemiddelde is van de $y$-waarnemingen, dus  $\bar{x}= \sum(x)/n$ en $\bar{y}= \sum(y)/n$

Na het berekenen van de helling wordt het intercept berekend door:
$$b_0 = \bar{y}− b_1$$

Het residu van observatie $i$ wordt berekend door:
$$e_i = y_i − \hat{y}_i$$
waar $Y_i$ de geobserveerde en $\hat{Y}_i$ de voorspelde waarde van observatie $i$ is

De standaardafwijking van de residuen wordt ook de standaard fout van de schatting genoemd (_standard error of estimate_) en wordt als volgt berekend.

$$ s_{est} = \frac{\sqrt{SS_{residual}}}{df} = \frac{\sqrt{\sum{{e_i}^2}}}{n-2}$$

Hierin staat _df_ voor het aantal vrijheidsgraden (_degrees of freedom_).

De standaard fout van de helling $b_1$(_standard error of the slope_) is dan
$$ s_{b1} = \frac{s_{est}}{ \sqrt{SS_x }} =\frac{s_{est}}{\sqrt{\sum{(x_i - \bar{x})^2}}} $$

Deze standaard fout $s_{b1}$ wordt gebruikt in de berekening van het betrouwbaarheidsinterval  voor de helling (_confidence interval for the slope_, $CI_{\beta_1}$)
De algemene formule voor het berekenen van een $C\%$ betrouwbaarheidsinterval van de helling $\beta_1$:
$$CI_{\beta_1} = (b_1 - t^* s_{b1}, b_1 + t^* s_{b1})$$

Hierin is $t^∗$ is de kritische waarde van de t(n-2) verdeling, zodanig dat de kans $P(−t^∗ ≤ t ≤ t^∗) = C/100$ 
Toelichting op t(n-2): de t-verdeling heeft 1 parameter als input, het aantal vrijheidsgraden. Dus deze notatie geeft de input voor de t-verdeling weer.

De standaard fout van het intercept $b_0$(_standard error of the intercept_) wordt berekend door 
$$ s_{b0} = s_{b1}\sqrt{ \frac{\sum{{x_i}^2}}{n}} $$

De berekening van het betrouwbaarheidsinterval voor het intercept $\beta_0$ is identiek aan die bij de helling:

Voor het berekenen van een $C\%$ betrouwbaarheidsinterval van het intercept $\beta_0$ gebruik je:
$$CI_{\beta_0} = (b_0 - t^* s_{b0}, b_0 + t^* s_{b0})$$

Hierin heeft $t^∗$ dezelfde waarde als eerder: is de kritische waarde van de t(n-2) verdeling, zodanig dat de kans $P(−t^∗ ≤ t ≤ t^∗) = C/100$ 

## R-code voor betrouwbaarheidsintervallen 'met de hand'

### helling
```
# haal benodigde gegevens uit het gefitte model & de data
slope <- coef(model)[2]
df <- length(x)-2
res <- model$residuals

# bereken parameters
se <- sqrt(sum(res^2)/(df))
s_slope <- se/sqrt( sum((x-mean(x))^2) )
tstar <- qt((cl+1)/2,df)  
# opmerking bij tstar:
#    vanwege een tweezijdig interval, is bij een confidence level van 0.95
#    een input van 0.975 voor qt nodig

# ondergrens en bovengrens van betrouwbaarheidsinterval
ci_sl <- slope - s_slope * tstar
ci_su <- slope + s_slope * tstar

# print betrouwbaarheidsinterval
cat(cl, 'betrouwbaarheidsinterval helling: (', ci_sl, ',', ci_su, ')')
```

### intercept
```
# haal benodigde gegevens uit het gefitte model & de data
intercept <- coef(model)[1]
df <- length(x)-2
res <- model$residuals

# bereken parameters
se <- sqrt(sum(res^2)/(df))
s_slope <- se/sqrt( sum((x-mean(x))^2) )
s_int <- s_slope * sqrt(sum(x^2)/length(x))
tstar <- qt((cl+1)/2,df)

# ondergrens en bovengrens van betrouwbaarheidsinterval
ci_il <- intercept - s_int * tstar
ci_iu <- intercept + s_int * tstar

# print betrouwbaarheidsinterval
cat(cl, 'betrouwbaarheidsinterval intercept: (', ci_il, ',', ci_iu, ')')

```