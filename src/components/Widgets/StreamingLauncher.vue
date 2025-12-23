<template>
<div class="streaming-launcher-wrapper">
  <h3>{{ options.label || 'Streaming Services' }}</h3>
  <div class="services-grid">
    <a
      v-for="service in services"
      :key="service.name"
      :href="service.url"
      target="_blank"
      class="service-button"
    >
      <span class="service-name">{{ service.name }}</span>
    </a>
  </div>
</div>
</template>

<script>

import axios from 'axios';
import WidgetMixin from '@/mixins/WidgetMixin';
import { widgetApiEndpoints } from '@/utils/defaults';

export default {
  mixins: [WidgetMixin],
  name: 'StreamingLauncher',
  data() {
    return {
      results: null,
    };
  },
  props: {
    options: {
      type: Object,
      default: () => ({}),
    },
  },
  computed: {
  	services() {
  	  return this.options.services || [];
  	},
  },
}
</script>

<style scoped lang="scss">
.streaming-launcher-wrapper {
  padding: 1rem;
  background: var(--widget-base-color);
  border-radius: var(--curve-factor);

  h3 {
    margin: 0 0 1rem 0;
    color: var(--widget-text-color);
  }

  .services-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
    gap: 0.75rem;
  }

  .service-button {
  	padding: 1rem;
  	background: var(--background);
  	border: 1px solid var(--outline-color);
  	border-radius: var(--curve-factor);
  	text-decoration: none;
  	text-align: center;
  	transition: all 0.2s ease;

	&:hover {
	  background: var(--background-darker);
	  transform: translateY(-2px);
	  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
	}

	.service-name {
	  color: var(--widget-text-color);
	  font-weight: 500;
	  font-size: 1rem;
    }
  }
}
</style>
