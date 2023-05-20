#input 

## Model Checking
To determine if $KB \models \alpha$:
	- Enumerate all possible models
	- If in every model, $KB \models \alpha$ is True, then $KB \models \alpha$
	- Otherwise, it is not true

## Conversion to CNF
1. Elminate biconditionals
2. Elminate implications
3. Move NOT inwards using De Morgan's laws
4. Use distributive law to distribute v whenever possible