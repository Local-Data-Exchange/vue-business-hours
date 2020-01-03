<template>
  <select :name="optionName" @change="inputEventHandler" v-model="selected" required oninvalid="this.setCustomValidity('Please select hour or remove it or disable the day')" oninput="setCustomValidity('')">
    <option disabled v-show="isFirstRow(index) && onlyOneRow(hours)" value>{{
      defaultText
    }}</option>
    <option
      v-for="time in filteredTimes"
      :key="time"
      :value="time"
      :selected="time == selected"
      >{{ time | formatTime(hourFormat24) }}</option
    >
  </select>
</template>

<script>
import { helperMixin } from '../mixins/helperMixin';
import { formFieldMixin } from '../mixins/formFieldMixin';
export default {
  name: 'BusinessHoursSelect',

  mixins: [helperMixin, formFieldMixin]
};
</script>
