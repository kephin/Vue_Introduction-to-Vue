# Methods, Computed, & Watchers

They are all the different ways to working with data that are available to us.

### METHODS

Methods are bound to the Vue instance, they are incredibly useful for functions you would like to access in directives

Examples 1:

```html
<div id="app" :style="{ backgroundColor: `hsl(${x}, 80%, 50%)` }" mousemove="xCoordinate">
  <p>
    <button @click="increment">+</button>
    {{ counter }}
    <button @click="decrement">-</button>
  </p>
  <p>Pixels across: {{ x }}</p>
</div>
```

```js
new Vue({
  el: '#app',
  data() {
    return {
      counter: 0,
      x: 0,
    };
  },
  methods: {
    increment() { this.counter++; },
    decrement() { this.counter--; },
    xCoordinate(e) { this.x = e.clientX; },
  },
});
```

Example 2:

```html
<div id="app">
  <ul>
    <li v-for="comment in comments">{{ comment }}</li>
  </ul>
  <input
    v-model="newComment"
    v-on:keyup.enter="addComment"
    placeholder="Add a comment"
  >
</div>
```

```js
new Vue({
  el: '#app',
  data: {
    newComment: '',
    comments: ['Looks great Julianne!', 'I love the sea' ],
  },
  methods: {
    addComment() {
      this.comments.push(this.newComment);
      this.newComment = '';
    },
  },
});
```

Example 3:

```html
<div id="app">
  <form @submit.prevent="submitForm">
    <div>
      <label for="name">Name:</label><br>
      <input id="name" type="text" v-model="name" required/>
    </div>
    <div>
      <label for="email">Email:</label><br>
      <input id="email" type="email" v-model="email" required/>
    </div>
    <div>
      <label for="caps">HOW DO I TURN OFF CAPS LOCK:</label><br>
      <textarea id="caps" v-model="caps" required></textarea>
    </div>
    <button :class="[name ? activeClass : '']" type="submit">Submit</button>
    <div>
      <h3>Response from server:</h3>
      <pre>{{ response }}</pre>
    </div>
  </form>
</div>
```

```js
new Vue({
  el: '#app',
  data() {
    return {
      userID: 1,
      name: '',
      email: '',
      caps: '',
      response: '',
      activeClass: 'active',
    };
  },
  methods: {
    submitForm() {
      axios.post('//jsonplaceholder.typicode.com/posts', {
        userID: this.userID,
        name: this.name,
        email: this.email,
        caps: this.caps,
      }).then(response => {
        this.response = JSON.stringify(response, null, 2);
      }).catch(error => {
        this.response = 'Error: ' + error.response.status;
      });
    },
  },
});
```

Example 4:

```html
<div id="app">
  <h3>Sort titles by:
    <button @click="sortLowest">Lowest Rated</button>
    <button @click="sortHighest">Highest Rated</button>
  </h3>
  <table>
    <thead>
      <tr>
        <th v-for="key in columns">{{ key }}</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="entry in ratingsInfo">
        <td v-for="key in columns">{{ entry[key] }}</td>
      </tr>
    <tbody>
  </table>
</div>
```

```js
new Vue({
  el: "#app",
  data() {
    return {
      columns: ["title", "rating"],
      ratingsInfo: [
        { title: `White Chicks`, rating: 82 },
        { title: `Grey's Anatomy`, rating: 98 },
        // ... a lot of them
      ],
    };
  },
  methods: {
    sortLowest() {
      this.ratingsInfo.sort((a, b) => a.rating - b.rating);
    },
    sortHighest() {
      this.ratingsInfo.sort((a, b) => b.rating - a.rating);
    },
  },
});
```

### COMPUTED

Computed properties are calculations that will be cached and will only update when needed. Highly performant but use with understanding.

**Difference between computed and methods**:

|COMPUTED|METHODS|
|---|---|
|Runs only when a dependency has changed|Runs whenever an update occurs|
|Cached|Not cached|
|Should be used as a property, in place of data|Typically invoked from v-on/@, but flexible|
|By default getter only, but you can define a setter|Getter/setter|

Example 1:

```html
<div id="app">
  <h3>Search a title: <input v-model="filterText" /></h3>
  <h3>Sort titles by:
    <button @click="sortLowest">Lowest Rated</button>
    <button @click="sortHighest">Highest Rated</button>
  </h3>
  <table>
    <thead>
      <tr>
        <th v-for="key in columns">{{ key }}</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="entry in filteredFilms">
        <td v-for="key in columns">{{ entry[key] }}</td>
      </tr>
    <tbody>
  </table>
</div>
```

```js
new Vue({
  el: "#app",
  data() {
    return {
      columns: ["title", "rating"],
      ratingsInfo: [
        { title: `White Chicks`, rating: 82 },
        { title: `Grey's Anatomy`, rating: 98 },
        // ... a lot of them
      ],
    };
  },
  methods: {
    sortLowest() {
      this.ratingsInfo.sort((a, b) => a.rating - b.rating);
    },
    sortHighest() {
      this.ratingsInfo.sort((a, b) => b.rating - a.rating);
    },
  },
  computed: {
    filteredFilms() {
      let filter = new RegExp(this.filterText, 'i');
      return this.ratingsInfo.filter(el => el.title.match(filter));
    },
  },
});
```

>Before moving into WATCHERS, we need to know: WHAT IS REACTIVE?

- Reactive programming is programming with asynchronous data streams.
- A stream is a sequence of ongoing events ordered in time that offer some hooks with which to observe it.
- When we use reactive premises for building applications, this means it's very easy to update state in reaction to events.

**Nice posts**:

1. [The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
1. [How to build a reactive engine in JavaScript. Part 1: Observable objects](https://www.monterail.com/blog/2016/how-to-build-a-reactive-engine-in-javascript-part-1-observable-objects)
2. [Vue's Reactivity in Depth](https://vuejs.org/v2/guide/reactivity.html)

### WATCHERS (Vue's Reactivity System)

Good for **asynchronous** updates and updates/transitions with data changes.

The Vue instance is the middleman between the DOM and the business logic. Watch updates the DOM only if it's required. Performing calculations in JS is really performant but accessing the DOM is not. So we have a Virtual DOM which is like a copy, but parsed in JavaScript.

Example:

```html
<div id="app">
  <section>
    <h1>🍺 Make yourself some Punk Beers 🍻</h1>
    <div v-if="beers.length === 0" class="loading">Loading...</div>
    <div v-for="beer in beers" class="beer-contain">
      <div class="beer-img">
        <img :src="beer.img" height="350" />
      </div>
      <div class="beer-info">
        <h2>{{ beer.name }}</h2>
        <p class="bright">{{ beer.tagline }}</p>
        <p><span class="bright">Description:</span> {{ beer.desc }}</p>
        <p><span class="bright">Tips:</span> {{ beer.tips }}</p>
        <h3 class="bright">Food Pairings</h3>
        <ul>
          <li v-for="item in beer.food">
            {{ item }}
          </li>
        </ul>
      </div>
    </div>
  </section>
</div>
```

```js
new Vue({
  el: '#app',
  data() {
    return {
      bottom: false,
      beers: [],
    };
  },
  watch: {
    bottom() {
      if (this.bottom) {
        this.addBeer();
      }
    },
  },
  created() {
    window.addEventListener('scroll', () => {
      this.bottom = this.bottomVisible();
    })
    this.addBeer();
  },
  methods: {
    bottomVisible() {
      const scrollY = window.scrollY;
      const visible = document.documentElement.clientHeight;
      const pageHeight = document.documentElement.scrollHeight;
      const bottomOfPage = visible + scrollY >= pageHeight;
      return bottomOfPage || pageHeight < visible;
    },
    addBeer() {
      axios.get('https://api.punkapi.com/v2/beers/random')
        .then(response => {
          let api = response.data[0];
          let apiInfo = {
            name : api.name,
            desc : api.description,
            img : api.image_url,
            tips : api.brewers_tips,
            tagline : api.tagline,
            food : api.food_pairing,
          };
          this.beers.push(apiInfo);
          if (this.bottomVisible()) {
            this.addBeer();
          }
      });
    },
  },
});
```

We also have access to the new value and the old value

```js
watch: {
  watchedProperty (value, oldValue) {
    //your dope code here
  }
},
```

We can also gain access to nested values with 'deep':

```js
watch: {
  watchedProperty {
    deep: true,
    nestedWatchedProperty (value, oldValue) {
      //your dope code here
    }
  }
},
```
