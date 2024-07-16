<template>
  <b-card no-body :header="header">
    <b-card-body>
      <p>{{ $t('admin.cleanup_desc') }}</p>
      <c-switch
        id="isCleanupEnabled"
        color="primary"
        v-model="isCleanupEnabled"
        label
        v-bind="labelIcon"
      />{{ $t('admin.cleanup_enabled') }}
      <b-validated-input-group-form-input
        id="versionMatchRegex"
        :label="$t('admin.cleanup_version_match_regex')"
        input-group-size="mb-3"
        type="text"
        v-model="versionMatchRegex"
        :tooltip="$t('admin.cleanup_version_match_regex_desc')"
      />
      <b-validated-input-group-form-input
        id="afterDays"
        :label="$t('admin.cleanup_after_days')"
        input-group-size="mb-3"
        rules="required"
        type="number"
        v-model="afterDays"
        lazy="true"
        :tooltip="$t('admin.cleanup_after_days_desc')"
      />
    </b-card-body>
    <b-card-footer>
      <b-button variant="outline-primary" class="px-4" @click="saveChanges">{{
        $t('message.update')
        }}</b-button>
    </b-card-footer>
  </b-card>
</template>

<script>
import { Switch as cSwitch } from '@coreui/vue';
import { ValidationObserver } from 'vee-validate';
import BValidatedInputGroupFormInput from '../../../forms/BValidatedInputGroupFormInput';
import common from '../../../shared/common';
import configPropertyMixin from '../mixins/configPropertyMixin';

export default {
  mixins: [configPropertyMixin],
  props: {
    header: String,
  },
  components: {
    cSwitch,
    ValidationObserver,
    BValidatedInputGroupFormInput,
  },
  data() {
    return {
      isCleanupEnabled: false,
      versionMatchRegex: '\\d+\\.\\d+\\.\\d+\\.\\d+-SNAPSHOT',
      afterDays: '90',
      labelIcon: {
        dataOn: '\u2713',
        dataOff: '\u2715',
      },
    };
  },
  methods: {
    saveChanges: function () {
      this.updateConfigProperties([
        {
          groupName: 'cleanup',
          propertyName: 'enabled',
          propertyValue: this.isCleanupEnabled,
        },
        {
          groupName: 'cleanup',
          propertyName: 'versionMatchRegEx',
          propertyValue: this.versionMatchRegex,
        },
        {
          groupName: 'cleanup',
          propertyName: 'afterDays',
          propertyValue: this.afterDays,
        },
      ]);
    },
  },
  created() {
    this.axios.get(this.configUrl).then((response) => {
      let configItems = response.data.filter(function (item) {
        return item.groupName === 'cleanup';
      });

      for (const element of configItems) {
        let item = element;
        switch (item.propertyName) {
          case 'enabled':
            this.isCleanupEnabled = common.toBoolean(item.propertyValue);
            break;
          case 'versionMatchRegEx':
            this.versionMatchRegex = item.propertyValue;
            break;
          case 'afterDays':
            this.afterDays = common.toBoolean(item.propertyValue);
            break;
        }
      }
    });
  },
};
</script>
