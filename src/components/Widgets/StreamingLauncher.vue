<template>
<div class="streaming-launcher-wrapper">
  <h3 v-if="options.label">{{ options.label }}</h3>
  <div :class="['services-container', layoutClass]" :style="gridStyle">
    <a
      v-for="service in services"
      :key="service.name"
      :href="service.url"
      target="_blank"
      :class="['service-button', { 'icon-failed': imageErrors[service.name] }]"
    >
      <img
        v-if="!imageErrors[service.name]"
        :src="getIconUrl(service)"
        :alt="service.name"
        class="service-icon"
        @error="handleImageError(service.name, $event)"
      />
      <span class="service-name" v-if="imageErrors[service.name] || (options.showLabels !== false)">
        {{ service.name }}
      </span>
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
      imageErrors: {},
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
  	layout() {
  	  // options: 'grid', 'horizontal', 'vertical'
  	  return this.options.layout || 'grid';
  	},
  	layoutClass() {
  	  return `layout-${this.layout}`;
  	},
  	gridColumns() {
  	  if (this.layout !== 'grid') return null;

  	  const count = this.services.length;
  	  const maxCols = this.options.maxColumns || 3;  // default to 3 if not specified

      // creates the grid based on the number of services if `maxColumns` > count
  	  return Math.min(maxCols, count);
  	},
  	gridStyle() {
  	  if (this.layout === 'grid' && this.gridColumns) {
  	  	return {
  	  	  gridTemplateColumns: `repeat(${this.gridColumns}, 1fr)`,
  	  	};
  	  }
  	  return {};
  	},
  },
  methods: {
    getIconUrl(service) {
      // if service has custom icon url, use that
      if (service.icon && typeof service.icon === 'string') {
      	return service.icon;
      }

      // extract domain from url
      let domain;
      try {
      	domain = new URL(service.url).hostname;
      } catch (e) {
      	return '';
      }

      // use allesedv favicon API as default provider (most reliable)
      return `https://f1.allesedv.com/128/${domain}`;
    },
    handleImageError(serviceName, event) {
      this.$set(this.imageErrors, serviceName, true);
      event.target.style.display = 'none';
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

  .services-container {
  	display: grid;
  	gap: 0.75rem;

  	// grid layouts (dynamic cols)
  	&.layout-grid {
  	  // cols set via inline style based on service count
  	}

  	// horizontal layout (single row, scrollable)
  	&.layout-horizontal {
  	  grid-auto-flow: column;
  	  grid-auto-flow: minmax(120px, 1fr);
  	  overflow-x: auto;

  	  &::-webkit-scrollbar {
  	  	height: 6px;
  	  }

  	  &::-webkit-scrollbar-track {
  	  	background: var(--background);
  	  	border-radius: 3px;
  	  }

  	  &::-webkit-scrollbar-thumb {
  	  	background: var(--outline-color);
  	  	broder-radius: 3px;
  	  }
  	}

  	// vertical layout (single column)
  	&.layout-vertical {
  	  grid-template-columns: 1fr;
  	}
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
  	display: flex;
  	flex-direction: column;
  	align-items: center;
  	justify-content: center;
  	gap: 0.5rem;
  	min-height: 80px;
  	transition: all 0.2s ease;

	&:hover {
	  background: var(--background-darker);
	  transform: translateY(-2px);
	  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
	}

	.service-icon {
	  width: 48px;
	  height: 48px;
	  object-fit: contain;
	}

	.service-name {
	  color: var(--widget-text-color);
	  font-weight: 500;
	  font-size: 1rem;
    }

    // when icon fails to load, show only text
    &.icon-failed {
      .service-name {
      	font-size: 1.1rem;
      }
    }
  }

  // responsive adjustments
  @media (max-width: 768px) {
  	.services-container.layout-grid {
  	  grid-template-columns: repeat(2, 1fr) !important;
  	}
  }
}
</style>
