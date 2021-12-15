### Carousels simplified with flexbox ###

#### Why flexbox ####
> It allows control without dependency on legacy features like top, left, float etc.

##### Ex display equally sized rows #####
```
_________________________________________________________________________________
|
||----------------------------------------------------------------
|
|Row 1 Row 1 Row 1 Row 1 Row 1 Row 1 Row 1 Row 1 Row 1 Row 1
|----------------------------------------------------------------
|
|Row 2 Row 2 Row 2 Row 2 Row 2 Row 2 Row 2 Row 2 Row 2 Row 2
|----------------------------------------------------------------
|
|Row 3 Row 3 Row 3 Row 3 Row 3 Row 3 Row 3 Row 3 Row 3 Row 3 
|
|-----------------------------------------------------------------
|_________________________________________________________________________________
````

>The above set of rows can be achieved with help of flexbox, scrollability can be accomplished using
`position` attribute

> outer container should have position RELATIVE
> inner container should have position ABSOLUTE

```
<style>
    .outer-relative-container {
        height: 400px; (fixes height at 400pixels, inner absolute container must have large value to allow for scrolling)
        position: relative;
        overflow: hidden;
    }

    .inner-absolute-container {
        position: absolute;
        height: 1600px;
        display: flex;
        flex-direction:column; (vertically stacked instead of side by side)

        .row {
            border: 2px dotted blue;
            width: 100%;
            flex: 1 0  200px; 
            margin: 1em;
        }
        /*(flex 1 0 200px, 1st property of 1 is set to true, 2nd property of zero means shrink is false, 3rd property of 200px refers to flex-basis i.e how space should be allocated across rows, in this case flex-direction is set to column, flex-basis of 200px fixes the height of each element to 200px, thereby rendering equal height rows) */
    }
</style>
<div class="outer-relative-container">
  <div class="inner-absolute-container">
      <div class="row"> Row 1
      </div>
      <div class="row"> Row 1
      </div>
      <div class="row"> Row 1
      </div>
  </div>
</div>  
```

#### Use of position attributes ####
> The outer container must have position property set to relative, while the inner container should have position property set to absolute, this ensure the rows or vertical containers are rendered with its parent container and also allows for proper scrolling of child elements within their respective containers


