# vue-docgen-api
[![npm](https://img.shields.io/npm/v/vue-docgen-api.svg)](https://www.npmjs.com/package/vue-docgen-api)

`vue-docgen-api` is a toolbox to help extracting information from [Vue][] components, and generate documentation from it.

Use [babel][] and [jsdoc-api][] to compile the code and analyze the contents of the component extracting methods and props. The output is a JavaScript object.

## Install

Install the module directly from npm:

```
npm install vue-docgen-api --save-dev
```

## API

The tool can be used programmatically to extract component information and customize the extraction process:

```js
var vueDocs = require('vue-docgen-api');
var componentInfo = vueDocs.parse(filePath);
```

## Using JSDoc tags

You can use the following [JSDoc][] tags when documenting components, props and methods.

## PropTypes

### Example

For the following component

```html
<template>
  <table class="grid">
    <thead>
      <tr>
        <th v-for="key in columns"
          @click="sortBy(key)"
          :class="{ active: sortKey == key }">
          {{ key | capitalize }}
          <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
          </span>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="entry in filteredData">
        <td v-for="key in columns">
          {{entry[key]}}
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import { text } from './utils';

/**
 * This is an example of creating a reusable grid component and using it with external data.
 * @version 1.0.5
 * @author [Rafael](https://github.com/rafaesc92)
 * @since Version 1.0.1
 */
export default {
  name: 'grid',
  props: {

    /**
     * object/array defaults should be returned from a factory function
     * @version 1.0.5
     * @since Version 1.0.1
     * @see See [Wikipedia](https://en.wikipedia.org/wiki/Web_colors#HTML_color_names) for a list of color names
     * @link See [Wikipedia](https://en.wikipedia.org/wiki/Web_colors#HTML_color_names) for a list of color names
     */
    msg: {
      type: [String, Number],
      default: 'Ejemplo'
    },
    /**
     * describe data
     * @version 1.0.5
     */
    data: [Array],
    /**
     * get columns list
     */
    columns: [Array],
    /**
     * filter key
     * @ignore
     */
    filterKey: {
      type: String,
      default: 'example'
    }
  },
  data () {
    var sortOrders = {}
    this.columns.forEach(function (key) {
      sortOrders[key] = 1
    })
    return {
      sortKey: '',
      sortOrders: sortOrders
    }
  },
  computed: {
    filteredData: function () {
      var sortKey = this.sortKey
      var filterKey = this.filterKey && this.filterKey.toLowerCase()
      var order = this.sortOrders[sortKey] || 1
      var data = this.data
      if (filterKey) {
        data = data.filter(function (row) {
          return Object.keys(row).some(function (key) {
            return String(row[key]).toLowerCase().indexOf(filterKey) > -1
          })
        })
      }
      if (sortKey) {
        data = data.slice().sort(function (a, b) {
          a = a[sortKey]
          b = b[sortKey]
          return (a === b ? 0 : a > b ? 1 : -1) * order
        })
      }
      return data
    }
  },
  filters: {
    capitalize: function (str) {
      return str.charAt(0).toUpperCase() + str.slice(1)
    }
  },
  methods: {

    /**
   * Sets the order
   *
   * @public
   * @version 1.0.5
   * @since Version 1.0.1
   * @param {string} key Key to order
   * @returns {string} Test
   */
    sortBy: function (key) {
      this.sortKey = key
      this.sortOrders[key] = this.sortOrders[key] * -1
    },

    hiddenMethod: function(){

    }
  }
}
</script>

<style scoped>
.grid {
  margin-bottom: 20px;
}
</style>
```

we are getting this output:

```json
{
  "description": "This is an example of creating a reusable grid component and using it with external data.",
  "methods": [
    {
      "name": "sortBy",
      "comment": "/**\n   * Sets the order\n   *\n   * @public\n   * @version 1.0.5\n   * @since Version 1.0.1\n   * @param {string} key Key to order\n   * @returns {string} Test\n   */",
      "modifiers": [],
      "params": [
        {
          "name": "key",
          "description": "Key to order",
          "type": {
            "name": "string"
          }
        }
      ],
      "returns": {
        "description": "Test",
        "type": {
          "name": "string"
        }
      },
      "description": "Sets the order",
      "tags": {
        "access": [
          {
            "title": "access",
            "description": "public"
          }
        ],
        "params": [
          {
            "title": "param",
            "description": "Key to order",
            "name": "key",
            "type": {
              "type": "NameExpression",
              "name": "string"
            }
          }
        ],
        "returns": [
          {
            "title": "returns",
            "description": "Test",
            "type": {
              "type": "NameExpression",
              "name": "string"
            }
          }
        ],
        "since": [
          {
            "title": "since",
            "description": "Version 1.0.1"
          }
        ],
        "version": [
          {
            "title": "version",
            "description": "1.0.5"
          }
        ]
      }
    }
  ],
  "displayName": "Grid",
  "props": {
    "msg": {
      "type": {
        "name": "string|number"
      },
      "required": "",
      "defaultValue": {
        "value": "\"Ejemplo\"",
        "computed": false
      },
      "tags": {
        "see": [
          {
            "title": "see",
            "description": "See [Wikipedia](https://en.wikipedia.org/wiki/Web_colors#HTML_color_names) for a list of color names"
          }
        ],
        "since": [
          {
            "title": "since",
            "description": "Version 1.0.1"
          }
        ],
        "version": [
          {
            "title": "version",
            "description": "1.0.5"
          }
        ],
        "link": [
          {
            "title": "link",
            "description": "See [Wikipedia](https://en.wikipedia.org/wiki/Web_colors#HTML_color_names) for a list of color names"
          }
        ]
      },
      "comment": "/**\n     * object/array defaults should be returned from a factory function\n     * @version 1.0.5\n     * @since Version 1.0.1\n     * @see See [Wikipedia](https://en.wikipedia.org/wiki/Web_colors#HTML_color_names) for a list of color names\n     * @link See [Wikipedia](https://en.wikipedia.org/wiki/Web_colors#HTML_color_names) for a list of color names\n     */",
      "description": "object/array defaults should be returned from a factory function"
    },
    "data": {
      "type": {
        "name": "array"
      },
      "description": "describe data"
    },
    "columns": {
      "type": {
        "name": "array"
      },
      "description": "get columns list"
    }
  },
  "comment": "/**\n * This is an example of creating a reusable grid component and using it with external data.\n * @version 1.0.5\n * @author [Rafael](https://github.com/rafaesc92)\n * @since Version 1.0.1\n */",
  "tags": {
    "author": [
      {
        "title": "author",
        "description": "[Rafael](https://github.com/rafaesc92)"
      }
    ],
    "since": [
      {
        "title": "since",
        "description": "Version 1.0.1"
      }
    ],
    "version": [
      {
        "title": "version",
        "description": "1.0.5"
      }
    ]
  }
}
```

## Change log

The change log can be found on the [Releases page](https://github.com/vue-styleguidist/vue-docgen-api/releases).

## Authors and license

[Rafael Escala](https://github.com/rafaesc92)

MIT License.


[JSDoc]: http://usejsdoc.org/
[vue]: https://vuejs.org/
[babel]: https://www.npmjs.com/package/babel-core
[jsdoc-api]: https://www.npmjs.com/package/jsdoc-api
