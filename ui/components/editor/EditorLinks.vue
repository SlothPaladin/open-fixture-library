<template>
  <div class="links">
    <EditorLink
      v-for="link of links"
      :key="link.uuid"
      ref="links"
      :link="link"
      :can-remove="links.length > 1"
      :formstate="formstate"
      @set-type="updateLinkProperty(link, `type`, $event)"
      @set-url="updateLinkProperty(link, `url`, $event)"
      @remove="removeLink(link)" />

    <a href="#add-link" @click.prevent="addLink()">
      <OflSvg name="plus" />
      Add link
    </a>
  </div>
</template>

<script>
import { arrayProp, objectProp } from 'vue-ts-types';
import { getEmptyLink } from '../../assets/scripts/editor-utils.js';

import EditorLink from './EditorLink.vue';

export default {
  components: {
    EditorLink,
  },
  inheritAttrs: false,
  model: {
    prop: `links`,
  },
  props: {
    links: arrayProp().required,
    formstate: objectProp().required,
  },
  methods: {
    async addLink() {
      const newLinks = [...this.links, getEmptyLink()];
      this.$emit(`input`, newLinks);

      await this.$nextTick();
      this.$refs.links[newLinks.length - 1].focus();
    },
    updateLinkProperty(updateLink, key, value) {
      const updatedLink = {
        ...updateLink,
        [key]: value,
      };

      this.$emit(`input`, this.links.map(
        link => (link === updateLink ? updatedLink : link),
      ));
    },
    removeLink(removeLink) {
      this.$emit(`input`, this.links.filter(
        link => link !== removeLink,
      ));
    },
  },
};
</script>

