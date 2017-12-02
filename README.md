# Using SASS and Create-React-App

## What is SASS

SASS is a tool that lets your write CSS like code that will compile back into CSS. SASS has a few features that make it worth the effort, like nesting classes and variables.

SASS has two different versions that you can use, SCSS and Sass. Sass does not use brackets to specify what properties to each class, but instead relies on you perfectly tabbing each tag to apply to the class. SCSS uses brackets and can be a bit easier to track what is going on. In this overview we will be using SCSS.

## Installing SASS

First you will need to install the SASS loader into your react App. To do that run the following command and install two packages.

`npm install --save node-sass-chokidar npm-run-all`

After installing `node-sass-chokidar` and `npm-run-all` you will need to change the scripts that are in your package.json.

After you install those two packages go to your package.json file and add these two lines to the top of the scrips list,

<pre>
"build-css": "node-sass-chokidar src/ -o src/",
"watch-css": "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive",
</pre>

Then you will need to change the start and build scripts while also adding on this `start-js` script.

<pre>
"start-js": "react-scripts start",
"start": "npm-run-all -p watch-css start-js",
"build": "npm run build-css && react-scripts build",
</pre>

Now when your `run npm start` or `yarn start` it will compile all the SCSS and Sass files into css files.

## SASS file structure

When using SASS we often refer to the what ever file uses SASS or SCSS as a SASS file. Below I refer to the process as SASS but the files will use the SCSS extension.

When using SASS by default it will make a css version of all your SCSS files. This can be useful, however it can be annoying to load each file individually to each component. It would be easier to compile all the files into one css file and importing once into your index.js file.

Take a look at the styles folder in this repo. You will notice that I have all the SCSS files names starting with an `_`. By doing this the compiler will ignore these files. We are going to make one file that chooses the load order of all our SCSS files, called main.scss. The reason for this is that the compiler will work in alphabetical order, and files like our reset.scss and variables.scss need to come before the other files. Also SCSS loads everything top to bottom so if our reset file came in the middle it would undo most of the styles above it.

I like to make one file for each component or route that I am working on so I have made a folder for each one. Again those files have the `_` in front of the file name.

Finally I have the main.scss file that only has import statements. This way we can choose what files to load first. Every time you make a new .scss file you will need to import it into the main.scss. When you run `npm start` it will create a file called main.css. This is the file you will import into component. You should only need to import it once into your index.js.

## Using SASS

The best location to learn SASS is their docs at http://sass-lang.com/guide. But we will go over a few examples to get you started

### Variables

In SASS you can create variables that can be used anywhere in your other files. This is very useful as it allows you to change large portions of your styles with out having to hunt down every instance of that color, or font. You can just change it in one place and every place it is used will update.

Below is an example of how to use variables. You will notice I have all my variables in one document for easy access.

<pre>
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
</pre>

### Mixins

Mixins are similar to functions in the way you write them but they make it so you can cut down on the amount of css you have to write. For instance often time when I use the width property I also use the height property. To save a bit of time I made a mixin that will do both in one line. I also made one for when I want to use flex, as there are often 3 properties I use together. Rather than add all three properties I would use the one mixin to take care of all 3 lines. Example below.

<pre>
//mixin declarations

@mixin flexSetUp($justify: flex-start, $align: flex-start, $direction: row) {
   display: flex;
   justify-content: $justify;
   align-items: $align;
   flex-direction: $direction
}

@mixin width-height($width, $height) {
   width: $width;
   height: $height;
}

//implementation

.flex {
  @include flexSetUp(space-around, center);
  @include width-height(800px, 150px);
  background: blue;
  margin: auto;
}

.flex-item {
  @include width-height(120px, 120px);
  background: $primary-color;
}
</pre>

You will notice on the `@mixin` declaration there is a colon with a value after the name; that is the default value if one is not specified when using the mixin.

### Nesting

Another great feature of SASS is the ability to nest classes inside of each other. This allows us to avoid using the `>` immediate child of css tag. When we nest a class in another class the styles will need bleed over into other styles. This can help keep your naming convention very simple as you can use the same class names as long as you nest them inside other classes.

<pre>
.container {
  background: blue;
  margin: 20px auto;

  .item {
    @include width-height(20%, 120px);
    background: green;
  }
}

.item {
  background: pink;
  @include width-height(420px, 130px);
  margin: 20px auto;
}
</pre>

In the above example the two `.item` classes will not conflict with each other, because the top one is nested inside of another class.

### Math

Finally we have the ability to do math insdie of css with the need for calc(). If we wanted to make a box exactly 1/3 the size of a parent container we can just type `width: 100% / 3;` and our container will be exactly 1/3 the size of its parent.

<pre>
.thirds {
  margin: 15px;
  width: 300px / 960px * 100%;
}
</pre>

You can also see that we can string together multiple operations.