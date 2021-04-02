## Accessibility: Usage of `alt` attribute and text descriptions when using img elements

A few days ago, I was reading some accessibility guidelines regarding the usage of **images** in web applications, and I learned a few interesting things which I wanted to share with all of you.

But before we jump to the topic, I must say that the more I read about accessibility, the more I am convinced that `accessibility is not an option, it is imperative`.

## Foreword

As the famous saying goes:

> A picture is worth a thousand words

*But there are people who will need those thousand words because they can't see the picture partially or completely.*

## Problem
We all know that images are a core part of every web application, but oftentimes, images are not made accessible which may affect a lot of users of your application.

> For most cases, making images accessible is pretty simple

While using images in our web applications, generally we don't realize that images fall into certain categories/types based on the use cases, and we should handle each use case differently.

## Types of Images
The following are the most commonly known types of images used in web applications.

1. Informative
2. Decorative
3. Functional
4. Group
5. Text-as-image
6. Image Map
7. Complex

Let's take a look at each of these types briefly and how to make images accessible in each use case.

### 1. Informative
These are the images that represent a concept or a piece of product information, e.g., pictures of people or places, or illustrations.

For such images, the `alt` text must be descriptive enough to explain the image completely.

Please take time for this and write a proper description for users that use screen readers or assistive technologies, to understand the context.

### 2. Decorative
These are the images that are only used to add visual decoration. They don't convey any information and they are not required to understand the page, e.g., icons, styling elements, images with adjacent descriptions.

Use `alt=""` for such images.

🚫 DO NOT omit `alt` attribute in any case.

### 3. Functional
Sometimes we use images as links or buttons. These are called functional images.

The `alt` text for these images should properly explain the functionality, e.g., "Click to submit the form" or "Click to open abc[dot]com in new window".

### 4. Group
There are situations where a group of images is used to represent one piece of information, e.g., a star rating where a few star icons are used to represent a rating.

In such cases, set the `alt` text of the first image to describe the entire collection and set `alt=""` for the rest of the items.

### 5. Text-as-image
Ideally, we should NOT use readable text in images. This is similar to the `Informative` use case, but more text content in the image.

But some use cases are pretty common where we present text as images, e.g., logos and article banners.

For logos, the `alt` text should be the name of the organization or product that the logo represents.

For use cases like banners, the `alt` text of the image should contain the exact text as that of the image.

### 6. Image Map
If you decide to use an image map, which means a single image divided by using multiple clickable `area` elements, you should make sure that the main image has a descriptive `alt` text that explains the overall context, and then each `area` should have its own descriptive alt text.

#### 7. Complex
These are the most tricky type of images. Graphs, charts, diagrams, bigger illustrations, maps are all examples of complex images.

You will need 2 pieces of description for such images, one summarized and the other detailed.

Let's see the options we have.

**7.1**: Add `alt` text with summary and use the `aria-describedby` attribute to point to an element with detailed information.

**7.2**: Add `alt` text with summary and use the `longdesc` attribute to point to a URI or section of a page with detailed information.

**7.3**: Use `figure` element with `role="group"` to wrap the image and use a nested `figcaption` element to list down all the details.

**7.4**: Add `alt` text with summary and add a link to complete description or actual description beside the image, based on the situation.

## Summary
Last but not least, DO NOT use words like **"image"**, **"photo"** or **"picture"** in the alt text, e.g., `image of a dog`. The screen readers will infer the `<img>` and read something like **"image image of a dog"**. This is definitely NOT what your user will like.

As seen in all of the above use cases, there is absolutely no way that we can omit the  `alt` text. In fact, we must use it with proper and meaningful descriptions.

---

I hope you enjoyed this quick tip and learned something. I would love to hear your valuable feedback.

Happy coding!