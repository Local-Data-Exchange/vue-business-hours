<template>
  <div class="business-hours-container">
    <business-hours
      :days="formatedDays"
      :name="name"
      :time-increment="timeIncrement"
      :type="type"
      :color="color"
      :localization="localization"
      :switch-width="switchWidth"
      :hour-format24="hourFormat24"
    ></business-hours>
  </div>
</template>

<script>
import BusinessHours from './BusinessHours.vue';
import moment from 'moment';
export default {
  name: 'BusinessHoursWrapper',
  components: {
    BusinessHours
  },
  props: {
    value: {
      type: Object,
      required: true
    },
    // days: {
    //   type: Object,
    //   required: true
    // },
    name: {
      type: String,
      default: 'businessHours'
    },
    type: {
      type: String,
      default: 'datalist',
      validator: function(value) {
        return ['datalist', 'select'].indexOf(value) !== -1;
      }
    },
    timeIncrement: {
      type: Number,
      default: 30,
      validator: function(value) {
        return [15, 30, 60].indexOf(value) !== -1;
      }
    },
    color: {
      type: String,
      default: '#2779bd',
      validator: function(value) {
        return value.charAt(0) === '#' ? true : false;
      }
    },
    localization: {
      type: Object,
      default: () => ({
        switchOpen: 'Open',
        switchClosed: 'Closed',
        placeholderOpens: 'To',
        placeholderCloses: 'From',
        addHours: 'Add hours',
        open: {
          invalidInput:
            'Please enter an opening time in the 12 hour format (ie. 08:00 AM). You may also enter "24 hours".',
          greaterThanNext:
            'Please enter an opening time that is before the closing time.',
          lessThanPrevious:
            'Please enter an opening time that is after the previous closing time.',
          midnightNotLast:
            "Midnight can only be selected for the day's last closing time."
        },
        close: {
          invalidInput:
            'Please enter a closing time in the 12 hour format (ie. 05:00 PM). You may also enter "24 hours" or "Midnight".',
          greaterThanNext:
            'Please enter a closing time that is after the opening time.',
          lessThanPrevious:
            'Please enter a closing time that is before the next opening time.',
          midnightNotLast:
            "Midnight can only be selected for the day's last closing time."
        },
        t24hours: '24 hours',
        midnight: 'Midnight'
      })
    },
    switchWidth: {
      type: Number,
      default: 90
    },
    hourFormat24: {
      type: Boolean,
      default: false
    }
  },
  data() {

    const converter = () => {
      const weekdays = ['sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday'];
      const randomKey = n => {
        let scope = "abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
        let getRandom = () => scope.charAt(Math.floor(Math.random()*scope.length));
        return Array.from((new Array(n)).keys()).map(getRandom).join("");
      };
      return weekdays.reduce((json, k) => {
        if(this.value.hasOwnProperty(k)) {
          json[k] = this.value[k].map(timeObj => {
            return {
              open: timeObj.open.split(":").join(""),
              close: timeObj.close.split(":").join(""),
              id: randomKey(7),
              isOpen: true
            }
          })
        } else {
          json[k] = [{open:"", close:"", isOpen: false, id: randomKey(7)}]
        }
        /* eslint-disable */
        console.log(json)
        return json;
      }, {});
    }

    return {
      formatedDays: converter()
    }
  },
  watch: {
    formatedDays: {
      handler() {

        const conv = old => {

          let dt = moment(old, 'HHmm');
          return isNaN(dt) ? '': dt.format('HH:mm');

          // let rx = /(?<h>\d{2})(?<m>\d{2})/;
          // if(rx.test(old)) {
          //   let {h,m} = old.match(rx).groups;
          //   old = h + ":" + m;
          // }
          // return old;
        }

        const format = (json, day) => {
          json[day] = this.formatedDays[day].map(item => {
            return {
              open: conv(item.open),
              close: conv(item.close)
            };
          });
          return json;
        }


        const cond = day => this.formatedDays[day].reduce((bool, item) => (bool && item.isOpen), true);
        let op = Object.keys(this.formatedDays).filter(cond).reduce(format, {});
        this.$emit('input', op);
      },
      deep: true
    }
  }

};
</script>

<style scoped>
.business-hours-container {
  display: block;
  width: 100%;
  /* max-width: 630px; */
  font-family: -apple-system, Helvetica, Arial, sans-serif;
  font-size: 15px;
  color: #3d4852;
}
</style>
