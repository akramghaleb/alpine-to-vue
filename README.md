# Converting Tailwind UI Alpine transitions to Vue transitions

After you copy a component from the Tailwind UI library and begin to adapt it from Vue JS to Alpine JS .. you may wonder what to do about the transitions. As I'm exploring this myself, I am documenting it for others in the same boat.

### Things to be aware of:
- Alpine calls the beginning and ending states "start" & "end"
- Vue calls the beginning and ending states "from" and "to"
- Alpine has inline "directives" ie x-transition:enter="classes"
- Vue has a wrapper component <transition> that applies classes to the child
- Alpine applies the classes you pass it for each state, :enter-start="class"
- Vue applies default classes to the child element, class="enter-start"
- Vue's <transition> component has props for changing default classes
- Vue does not have native click away support 

So with all that in mind, I think a simple way to quickly transition from Alpine JS to Vue JS transitions would be the following steps.

**1: Change the inline syntax to stacked**

Multi select in your IDE and put each x-transition on a new line

```html
<div
    x-transition:enter="transition ease-out duration-200"
    x-transition:enter-start="transform opacity-0 scale-95">
  ```

**2: Change "-start" to "-from" and "-end" to "-to"**

As previously mentioned, Vue uses "from" and "to" not "start" and "from"

**3: Remove x-transition: from each line**

We are converting these from Alpine directives to Vue static properties

```html
<div
    v-show="open"
    enter="transition ease-out duration-200"
    enter-from="transform opacity-0 scale-95">
```

**4: Move the props to a `<transition>` wrapper component**

```html
<transition
    enter="transition ease-out duration-200"
    enter-from="transform opacity-0 scale-95">
        <div v-show="open">...</div>
</transition>
```

**5: Add "-class" to the end of each prop**

Vue's `<transition>` component allows you to add the classes to apply to the child component at each point in the transition lifecycle. The property names perfectly line up and just require us to add '-class' to the end of each.

```html
<transition
    enter-class="transition ease-out duration-200"
    enter-from-class="transform opacity-0 scale-95">
        <div v-show="open">...</div>
</transition>
```

**6: Move `transform` to child element**

The transform needs to be added to the child element and can be removed from the transition properties


```html
<transition
    enter-class="ease-out duration-200"
    enter-from-class="opacity-0 scale-95">
        <div v-show="open" class="transform">...</div>
</transition>
```


# Click away handling

Alpine comes with a handy click.away handling. Vue does not.

You can register an event listener in mounted to handle this behavior like so:

**1: Add a ref="component_name" to the parent element within component**

Tell your component what html element it should watch clicks outside of

**2: Listen for clicks and check if the element is outside of the ref element.**

```js
mounted() {
    document.addEventListener('click', event => {    
        event.stopPropagation();
        if(!this.$refs.component_name.contains(event.target)) {
            this.open = false;
        }
    });
}
```
