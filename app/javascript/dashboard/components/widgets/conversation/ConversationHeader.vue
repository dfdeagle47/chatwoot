<template>
  <div class="conv-header">
    <div class="user">
      <Thumbnail
        :src="chat.meta.sender.thumbnail"
        size="40px"
        :badge="chat.meta.sender.channel"
        :username="chat.meta.sender.name"
      />
      <div class="user--profile__meta">
        <h3 class="user--name">
          {{ chat.meta.sender.name }}
        </h3>
        <button
          class="user--profile__button"
          @click="$emit('contactPanelToggle')"
        >
          {{ viewProfileButtonLabel }}
        </button>
      </div>
    </div>
    <div class="flex-container">
      <div class="multiselect-box ion-headphone">
        <multiselect
          v-model="currentChat.meta.assignee"
          :options="agentList"
          label="name"
          :allow-empty="true"
          deselect-label="Remove"
          placeholder="Select Agent"
          selected-label=""
          select-label="Assign"
          track-by="id"
          @select="assignAgent"
          @remove="removeAgent"
        >
          <span slot="noResult">{{ $t('AGENT_MGMT.SEARCH.NO_RESULTS') }}</span>
        </multiselect>
      </div>
      <ResolveButton />
    </div>
  </div>
</template>
<script>
/* eslint no-console: 0 */
/* eslint no-param-reassign: 0 */
/* eslint no-shadow: 0 */
/* global bus */

import { mapGetters } from 'vuex';
import Thumbnail from '../Thumbnail';
import ResolveButton from '../../buttons/ResolveButton';

export default {
  components: {
    Thumbnail,
    ResolveButton,
  },

  props: {
    chat: {
      type: Object,
      default: () => {},
    },
    isContactPanelOpen: {
      type: Boolean,
      default: false,
    },
  },

  data() {
    return {
      currentChatAssignee: null,
    };
  },

  computed: {
    ...mapGetters({
      agents: 'agents/getVerifiedAgents',
      currentChat: 'getSelectedChat',
    }),
    agentList() {
      return [
        {
          confirmed: true,
          name: 'None',
          id: 0,
          role: 'agent',
          account_id: 0,
          email: 'None',
        },
        ...this.agents,
      ];
    },
    viewProfileButtonLabel() {
      return `${this.isContactPanelOpen ? 'Hide' : 'View'}  Profile`;
    },
  },

  methods: {
    assignAgent(agent) {
      this.$store
        .dispatch('assignAgent', {
          conversationId: this.currentChat.id,
          agentId: agent.id,
        })
        .then(() => {
          bus.$emit('newToastMessage', this.$t('CONVERSATION.CHANGE_AGENT'));
        });
    },

    removeAgent() {},
  },
};
</script>
