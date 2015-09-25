React support for SourceJS
===============

[SourceJS](http://sourcejs.com) middleware to support [React](https://facebook.github.io/react/) markup language (`*.jsx`) instead of native `*.src`.

## Install

To install, run npm in `sourcejs/user` folder:

```
npm install sourcejs-react --save
```

In `sourcejs/user/options.js` you need to add `index.jsx` to `core.common.specFiles`:
```js
module.exports = {
    core: {
        common: {
            specFiles: [
                'index.jsx',
                'index.src',
                'index.src.html',
                'index.jade',
                'index.haml',
                'index.md',
                'readme.md',
                'README.md',
                'index.html'
            ]
        }
    }
    //...
};
```

Then restart your SourceJS application, middleware will be loaded automatically.

## Usage

After installation, instead of `index.src` pages, you can use `index.jsx` files with React markup.

index.jsx
```js
var React = require('react/addons');
var Button = require('button.jsx');
module.exports = React.createClass({
    render: function () {
        return (
            <div>
                <link rel="stylesheet" href="source-example.css" />

                <h1>Button</h1>

                <div className="source_info">
                    <p>Various button treatments.</p>
                </div>

                <section className="source_section">
                    <h2>Button (Purple)</h2>
                    <div className="source_example">
                        <Button>Btn Copy Gibson Reg; 1.2em</Button>
                    </div>
                </section>

                <section className="source_section">
                    <h2>Button (White)</h2>
                    <div className="source_example">
                        <Button modifier="white">Btn Copy Gibson Reg; 1.2em</Button>
                    </div>
                </section>
            </div>
        );
    }
});
```

## Isomorphic (client + server side) rendering

Note, you might get warnings that checksum are different when you try to use server and client side rendering together. That might happen because on server side (sourcejs) checksum will be created for the whole spec page, while you might be interested only in what goes in `section.source_example`. You need to force react to create a checksum in a following way (for example above):
```js
var React = require('react/addons');
var Button = require('button.jsx');
module.exports = React.createClass({
    render: function () {
        var factory = React.createFactory(Button);
        var button = React.renderToString(factory({
            children: 'Btn Copy Gibson Reg; 1.2em'
        }));
        var buttonWhite = React.renderToString(factory({
            modifier: 'white',
            children: 'Btn Copy Gibson Reg; 1.2em'
        }));
        return (
            <div>
                <link rel="stylesheet" href="source-example.css" />

                <h1>Button</h1>

                <div className="source_info">
                    <p>Various button treatments.</p>
                </div>

                <section className="source_section">
                    <h2>Button (Purple)</h2>
                    <div className="source_example" dangerouslySetInnerHTML={{__html: button}}></div>
                </section>

                <section className="source_section">
                    <h2>Button (White)</h2>
                    <div className="source_example" dangerouslySetInnerHTML={{__html: buttonWhite}}></div>
                </section>
            </div>
        );
    }
});
```

## Error handling

sourcejs-react will show errors right on the page when sourcejs is launched in development mode and log errors via console.error when in other (production) mode.

##Showing JSX markup instead of rendered markup
By default sourcejs use code inside `.source_example` to output source code, like on following screenshot:
![rendered code](https://cloud.githubusercontent.com/assets/3027415/8033889/b37faaaa-0de2-11e5-918e-76fb2ea84a22.png)
In order to show just JSX markup, instead of rendered markup, you can use `.src-html` and copy markup example there
```html
<section className="source_section">
    <h2>Button (Purple)</h2>
    <code className="src-html">
        {`<Button color="purple">Btn Copy Gibson Reg; 1.2em</Button>`}
    </code>
    <div className="source_example">
      <Button color="purple">Btn Copy Gibson Reg; 1.2em</Button>
    </div>
</section>
```

Alternatively you can import `sourcejs-react/SourceExample.jsx`:
```js
var SourceExample = require('sourcejs-react/SourceExample.jsx');
```
and use it like in example below inside render method, code block will be auto-generated for you:
```html
<section className="source_section">
    <h2>Button (Purple)</h2>
    <SourceExample extraClasses="you addition class names">
      <Button color="purple">Btn Copy Gibson Reg; 1.2em</Button>
    </SourceExample>
</section>
```

##Integration with browserSync plugin
To allow [browserSync](https://github.com/sourcejs/sourcejs-contrib-browser-sync) middleware work we need to make it load after react middleware. In user folder add following to `options.js`:
```js
core: {
    middlewares: {
        list: {
            'sourcejs-contrib-browser-sync': {
                order: 2
            },
            'sourcejs-react': {
                order: 1
            }
        }
    }
}
```
