<template>
<div class="hello-world-wrapper">
  <h3>Hello World Widget</h3>
  <p>This is my first custom Dashy widget!</p>
</div>
</template>

<script>

import axios from 'axios';
import WidgetMixin from '@/mixins/WidgetMixin';
import { widgetApiEndpoints } from '@/utils/defaults';

export default {
  mixins: [WidgetMixin],
  data() {
  	return {
  	  results: null,
  	};
  },
  computed: {  // this is where to define any accepted parameters from the user
    count() {  // this is an example of an optional parameter called 'count'
      if (!this.options.count) {
      	return 5;
      }
      return this.options.count;
    },
  	endpoint() {  // this is an example of a defined endpoint parameter from the user (required)
  	  // if this widget needs a data request, then the URL needs to be added to
  	  // `widgetApiEndpoint` array in `defaults.js` and imported above like
  	  // import { widgetApiEndpoints } from '@/utils/defaults';
  	  // the example call below targets the `myApi` key in `widgetApiEndpoints` and passes the
  	  // computed property `count` defined above
  	  return `${widgetApiEndpoints.myApi}?count=${this.count}`;
  	},
  },
  methods: {
  	fetchData() {  // this uses `axios` to make a data request call to our endpoint
  	  // if the response completes successfully, we'll pass the results to another function
  	  // if there's an error, we'll call `this.error()` which will display the message to user
  	  // whatever the result, once completed we call this.finishLoading() to hide the loader
  	  // the last 2 steps are not completed here yet...
  	  this.makeRequest(this.endpoint, this.headers).then(this.processData);
  	},
  	processData(data) {
  	  // do processing any here, and set component data
  	  this.results = data;
  	},
  },
	// name: 'HelloWorld',
};
</script>

<style scoped lang="scss">
.hello-world-wrapper {
  padding: 1rem;
  background: var(--widget-base-color);
  border-radius: var(--curve-factor);

  h3 {
    margin: 0 0 0.5rem 0;
    color: var(--widget-text-color);		
  }

  p {
  	margin: 0;
  	color: var(--widget-text-color);
  }
}
</style>
