## Web Animations and Accessibility

In the modern web era, animations are one of the core features of user interfaces. Only a few years ago, animations were only possible with JavaScript, and there was minimal support for animations in CSS. However, with the dawn of CSS 3, web animations have taken a huge leap and there are so many brilliant developers who can create astonishing user interfaces using CSS animations only.

There are so many common use cases of animations, including bulging buttons, carousels, parallax, and accordions. But there is a very important aspect to be considered while using animations, i.e. `Accessibility`.

Although animations look very cool, but that might not be the case with all of the users of your application. There are people who suffer from seasickness aka motion sickness, and some with vestibular motion disorder. These people will have a lot of problem using the interfaces with animations, and in some cases, the animations might affect them adversely.

### How to cope with this situation?

By taking this fact into consideration, pretty much all operating systems provide options to reduce motion, so the users see less movements while using their devices. This also applies to website or web applications they use in their favorite web browsers. 

And the good news is that all web browsers are enabled to access the user's accessibility settings and make it available to the developers, so they can write their CSS and JavaScript accordingly.

### CSS Animations

If you are using CSS animations, you can use the `prefers-reduced-motion` media query to read user's settings and disable/reduce your animations accordingly.

Here is how your CSS should look like.
```
// Supported by all browsers except IE
@media (prefers-reduced-motion: reduce) {
    // your styles with reduced or no animations
}
```
Or it can be only `@media (prefers-reduced-motion)` to keep things simpler.

### JavaScript Animations

In case you have animations implemented using JavaScript, you can use the `window.matchMedia` method to check for the value of `prefers-reduced-motion` setting.

Here is how your script should look like.
```
window.matchMedia('(prefers-reduced-motion)');
```
It will return an object of type [MediaQueryList](https://developer.mozilla.org/en-US/docs/Web/API/MediaQueryList) which looks like this:
```
{
  media: "(prefers-reduced-motion)",
  matches: false,
  onchange: null
}
```
The most important value here is the boolean property `matches`. If it has a value of `true`, you can disable/reduce animations safely. You can store this object in a class level or global storage so your application can access it.

> That is pretty much all you need to help users with seasickness or vestibular motion disorder to visit your website or web application without any problem.

But wait, there is one more thing that we can do to enhance it a little bit. What if the user toggles the reduce motion settings on the fly?

The CSS media query will respond immediately, but the JavaScript solution will require a page load because we're reading the value only once and storing it for reuse. This doesn't sound like a good user experience.

You might have noticed that there is a 3rd property in the `MediaQueryList` response, that is, `onchange`. This is an event, which is emitted by the browsers when the value of target media query is changed. We can simply add an event handler and listen to this even, and then update our application accordingly.

This is a very simple example of how your can do it.
```
const motionMediaMatch = window.matchMedia('(prefers-reduced-motion)');

function motionMediaMatchListener() {
  if (motionMediaMatch.matches) {
    // disable or reduce animations
  } else {
    // enable or resume animations
  }
}

motionMediaMatch.addEventListener('change', motionMediaMatchListener);
```

And we are done 🙌🏻

### Take Away

Accessibility is not an option, it is an imperative! We, developers, should seriously take accessibility in to consideration and make sure that whatever we build, it should be as accessible as possible.

Happy learning, happy coding!

> <span>Banner image by <a href="https://unsplash.com/@pankajpatel?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Pankaj Patel</a> on <a href="https://unsplash.com/s/photos/animation-accessibility?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>