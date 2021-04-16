## Better yet accessible :focus

Every element of your app that is interactive or needs to be highlighted, should have tabbing focus indicator.

By default, all browsers render an `outline` as a focus indicator BUT that is different per browser.

As an example, this is how Chrome, Firefox, and Safari will set focus on the same element.

![focus styling by different browsers.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1617174416408/kiCGx6klu.png)

Although every browser renders an `outline`, the color of that `outline` might not work well with your app theme or may not be prominently visible to your users.

> 🛑 This is not a good UX and it should be fixed!

The good news is that we can improve it with CSS. Let's see how.

The following rule will apply a `solid blue outline of 2px` to all focused elements on your page.

```
*:focus {
    outline: #007EFF solid 2px;
}
```

You can change the color based on your app theme or even for a section of the app by using more specific selectors.

> But wait, there is more!

The `outline` will always have corners regardless of the shape of the element. You might want to make it look better and follow the shape of the element.

The following snippet comes to the rescue:

```
*:focus {
    outline: transparent solid 2px;
    box-shadow: 0 0 0 2px #fff, 0 0 0 4px #007EFF;
}
```

This is the same button after a pinch of precise CSS (as @[Darshan Satya](@DashSat) says).

![focus styling by different browsers (1).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1617174814833/DqouVq45h.png)

You must be wondering why do we have a transparent `outline` and a colored `box-shadow`.

Well, the fact of the matter is that `box-shadow` is not visible in Windows High Contrast mode, and we need the `outline` so the `transparent` color can be overridden and used by the OS.

I hope you liked this tip and I will love to hear your feedback.

Happy coding! 