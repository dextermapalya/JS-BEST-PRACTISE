### REPLACE IF-ELSE HELL WITH MAP functions ###

#### Below is if-else hell ####
```
if (player1 === rock) {
        playHand(player2, score, scissors, paper);
    }

    if (player1 === paper) {
        playHand(player2, score, rock, scissors);
    }

    if (player1 === scissors) {
        playHand(player2, score, paper, rock);
    }

    if (score.player1 === 2) {
        winner = 1;
    }
```
### BRING IN THE MAP ###
> Now let’s replace those  IF else statements that map actions to lambda functions.
finally use call
```
function toggleDetailButtons() {

      var visibleState = {
              visible: function visible() {
                $(".detail_screen_buttons").hide();
                //return playHand(player2, score, scissors, paper);
                return
              },
              hidden: function hidden() {
                $(".detail_screen_buttons").show();
                return
              }
      };

      var visibility = $(".detail_screen_buttons").is(":hidden") ? 'hidden' : 'visible'
      visibleState[visibility].call();

}
```

#### Seems legit so far and also pretty common, right? ####
>In fact, there is a mistake in this piece of code. Examine the order of execution again.
>You access a remote URL.
>You wait for the response.
>You process the response.
>You call another function to process the response.
>In point 4 lies the problem. It is redundant. You already process the response.

#### The Solution ####
```
fetch("url").then(processResponse);
```
 > Working this way is called point-free or tacit. Its main feature is that you never specify the arguments for each function application.

 #### Be Aware When Working With Methods ####

 > A case where you should be in alarm mode is whenever you call an object’s method. If your original code looks similar to this:

```
fetch("url").then(function(data) {
    anObject.process(data);
});
```
> You can’t just transform the code to the following and expect it to work:
```
fetch("url").then(anObject.process);
```

#### The solution is to use .bind() ####
```
fetch("url").then(anObject.process.bind(anObject));
```
```
function doSome(data) {
    return anObject.process(data);
}
```
> Must be changed to:
```
const doSome = anObject.process.bind(anObject);
```

#### Syntax and programmatically correct but still wrong ####
```
const createdPromise = new Promise(resolve => {
  somePreviousPromise.then(result => {
    // do something with the result
    resolve(result);
  });
});
```

> The promise is wrapped within a new Promise making it slower

#### Fix for above problem ####
```
const createdPromise = new Promise((resolve, reject) => {
  somePreviousPromise.then(result => {
    // do something with the result
    resolve(result);
  }).catch(reject);
});
```
> This catches a possible error
