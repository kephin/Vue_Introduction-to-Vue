# Directives & Data Rendering

### DIRECTIVES

- V-FOR: Loops through a set of values. Can also do a static number.

```html
<li v-for='num in 5'>
  {{ num }}
</li>
```

- V-MODEL: Creates a relationship between the data in the instance/component and a form input, so you can dynamically update values.

```html
<div id="app">
  <textarea v-model="message"/></textarea>
  <p>{{ message }}</p>
</div>
```

Here, we write data as a function and return it. It is because when we start to break them into component, we need to have each component to have their own isolated scope.

```js
new Vue({
  el: '#app',
  data() {
    return {
      message: 'This is a good place to type things.',
    };
  },
});
```

The other way of using V-for:

Instead of this:

```html
<div id="app">
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john"> John</label>
  <input type="checkbox" id="paul" value="Paul" v-model="checkedNames">
  <label for="paul"> Paul</label>
  <input type="checkbox" id="george" value="George" v-model="checkedNames">
  <label for="george"> George</label>
  <input type="checkbox" id="ringo" value="Ringo" v-model="checkedNames">
  <label for="ringo"> Ringo</label>
  <span>Checked names: {{ checkedNames }}</span>
</div>
```

```js
new Vue({
  el: '#app',
  data: {
    checkedNames: [],
  },
});
```

We can refactor our code:

```html
<div id="app">
  <span v-for="option in options">
    <input type="checkbox" :id="option.value" :value="option.value" v-model="checkedNames">
    <label :for="option.value"> {{ option.value }}</label>
  </span>
  <span>Checked names: {{ checkedNames }}</span>
</div>
```

```js
new Vue({
  el: '#app',
  data: {
    checkedNames: [],
    options: [
      { value: 'John' },
      { value: 'Paul' },
      { value: 'George' },
      { value: 'Ringo' },
    ],
  },
});
```

- MODIFIERS:

  1. `v-model.trim` will strip any leading or trailing whitespace from the bound string
  2. `v-model.number` changes strings to number inputs
  3. `v-model.lazy` won’t populate the content automatically, it will wait to bind until an event happens. (It listens to change events instead of input)

- V-IF / V-SHOW: Is a conditional that will display information depending on meeting a requirement. This can be anything like buttons, forms, divs, components, etc.

The difference between V-IF and V-SHOW is that V-IF is completely unmounting and remounting elements, whereas V-SHOW is just toggling visiblility for us.

- V-IF / V-ELSE-IF / V-ELSE: Pretty straightforward you can conditionally render one thing or another.

- V-BIND (or : ): One of the most useful directives so there's a shortcut! We can use it for so many things like class and style binding, creating dynamic props, etc.

```html
<div id="app">
  <h3>What is your favorite kind of taco?</h3>
  <textarea v-model="tacos"></textarea>
  
  <br>
  <button :class="[tacos ? activeClass : '']">Let us know!</button>
</div>
```

```js
new Vue({
  el: '#app',
  data() {
    return {
      tacos: '',
      activeClass: 'active',
    },
  },
});
```

- V-ONCE and V-PRE: Not quite as useful, v-once will not update once it's been rendered. v-pre will print out the inner text exactly how it is, including code (good for documentation)

- V-ON (or @): Extremely useful so there's a shortcut! v-on is great for binding to events like click and mouseenter. You're able to pass in a parameter for the event like (e). We can also use ternaries directly

```html
<div id="app">
  <div class="item">
    <img src="https://amazonaws.com/backpack.jpg"/>
    <div class="quantity">
      <button @click="counter > 0 ? counter -= 1 : 0">-</button>
      <span>Quantity: {{ counter }}</span>
      <button @click="counter += 1">+</button>
    </div>
    <button class="submit" @click="">Submit</button>
  </div>
</div>
```

- MULTIPLE BINDINGS

```html
<div v-on="
  click   : onClick,
  keyup   : onKeyup,
  keydown : onKeydown
">
</div>
<!-- Or use shortcut -->
<div @="
  click   : onClick,
  keyup   : onKeyup,
  keydown : onKeydown
">
</div>
```

- MODIFIERS:

  1. @mousemove.stop is comparable to e.stopPropogation()
  2. @mousemove.prevent this is like e.preventDefault()
  3. @submit.prevent this will no longer reload the page on submission
  4. @click.once not to be confused with v-once, this click event will be triggered once.
  5. @click.native so that you can listen to native events in the DOM

- V-HTML: Great for strings that have html elements that need to be rendered! But don't use on user-rendered content, avoid XSS attacks

- V-TEXT: Similar to mustache templates

**Examples**:

```html
<div id="app">
  <div class="row">
    <input type="number" v-model.number='num1'>
    <span>+</span>
    <input type="number" v-model.number='num2'>
    <span>=</span>
    <span>{{ num1 + num2 }}</span>
  </div>
  <div class="row second">
    <select v-model='name'>
      <option>Red</option>
      <option>Yellow</option>
      <option>Blue</option>
      <option>Green</option>
    </select>
    <p v-if='name'>My favorite color is {{ name }}</p>
  </div>
</div>
```

```js
new Vue({
  el: '#app',
  data() {
    return {
      num1: 0,
      num2: 0,
      name: '',
    };
  },
});
```
