### Instant Invocation And Initialization IFFE ###

#### With Parameters ####
> You can also have the same style but passing some arguments to the function, which will be used as the initial values for its parameters:

```
(function(param1, param2) {
  // do whatever I want with the
  // received arguments param1 and param2...
})("Fire Ball", "Ice Bolt");
```

#### You could also return something from the function: ####
> Furthermore, the three inner functions wouldn’t even be visible to the rest of the code. This also helps to keep the global namespace clean:

```
var iffe = (function() {
    function castSpell() {
        console.log("Casting Fireball.");
    }

    function shootSpell() {
        console.log("Shooting Fireball.");
    }

    function doSpellDamage() {
        console.log("Do 111 Fire Damage.");
    }
    /* castSpell();
    shootSpell();
    doSpellDamage(); */
    return  {
    	c: castSpell,
      d: shootSpell,
      e: doSpellDamage
    }
})

//invoke
iffe().c()
```
