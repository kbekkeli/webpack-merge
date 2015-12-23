[![build status](https://secure.travis-ci.org/survivejs/webpack-merge.png)](http://travis-ci.org/survivejs/webpack-merge)
# webpack-merge - Merge designed for Webpack

Normal merge function isn't that useful with webpack configuration as it will override object keys and arrays by default. It is more beneficial to concatenate arrays instead. This little helper achieves just that. Consider the example below:

## API

```javascript
var output = merge(object1, object2, object3, ...);
```

## Example

**package.json**

```json
{
  "scripts": {
    "start": "webpack-dev-server",
    "build": "webpack"
  },
  ...
}
```

**webpack.config.js**

```javascript
var merge = require('webpack-merge');

var TARGET = process.env.npm_lifecycle_event;

var common = {
  entry: [path.join(ROOT_PATH, 'app/main.jsx')],
  ...
  module: {
    loaders: [
      {
        test: /\.css$/,
        loaders: ['style', 'css'],
      },
    ],
  },
};

if(TARGET === 'start') {
  module.exports = merge(common, {
    module: {
      // loaders will get concatenated!
      loaders: [
        {
          test: /\.jsx?$/,
          loader: 'babel?stage=1',
          include: path.join(ROOT_PATH, 'app'),
        },
      ],
    },
    ...
  });
}

if(TARGET === 'build') {
  module.exports = merge(common, {
    ...
  });
}

...
```

Check out [SurviveJS - Webpack and React](http://survivejs.com/) to dig deeper into the topic.

## Merging loaders

Webpack-merge tries to be smart about merging loaders. Loaders with matching tests will be merged into a single loader value.

Loader string values `loader: 'babel'` override each other.

```
merge({
  loaders: [{ test: /\.js$/, loder: 'babel' }]
}, {
  loaders: [{ test: /\.js$/, loder: 'coffee' }]
});
// will become
{
  loaders: [{ test: /\.js$/, loder: 'coffee' }]
}
```

Loader array values `loaders: ['babel']` will be merged, without duplication.

```
merge({
  loaders: [{ test: /\.js$/, loders: ['babel'] }]
}, {
  loaders: [{ test: /\.js$/, loders: ['coffee'] }]
});
// will become
{
  loaders: [{ test: /\.js$/, loders: ['babel', 'coffee'] }]
}
```

Loader query strings `loaders: ['babel?plugins[]=object-assign']` will be overridden

```
merge({
  loaders: [{ test: /\.js$/, loders: ['babel?plugins[]=object-assign'] }]
}, {
  loaders: [{ test: /\.js$/, loders: ['babel', 'coffee'] }]
});
// will become
{
  loaders: [{ test: /\.js$/, loders: ['babel', 'coffee'] }]
}
```

Loader arrays in source values will have loader strings merged into them.

```
merge({
  loaders: [{ test: /\.js$/, loder: 'babel' }]
}, {
  loaders: [{ test: /\.js$/, loders: ['coffee'] }]
});
// will become
{
  loaders: [{ test: /\.js$/, loders: ['babel', 'coffee'] }]
}
```

Loader strings in source values will always override.

```
merge({
  loaders: [{ test: /\.js$/, loders: ['babel'] }]
}, {
  loaders: [{ test: /\.js$/, loder: 'coffee' }]
});
// will become
{
  loaders: [{ test: /\.js$/, loder: 'coffee' }]
}
```

## Contributors

* [Fernando Montoya](https://github.com/montogeek) - Use separate lodash functions instead of the core package. Faster to install this way.

* [Jonathan Felchlin](https://github.com/GreenGremlin) - Smart merging for loaders.

## License

webpack-merge is available under MIT. See LICENSE for more details.

