<template>
	<p>Total Time: {{ formatedTimes }}</p>
	<div style="display: flex; justify-content: space-around;">
	  <div>
		<label for="minutes">Minutes:</label>
		<select value="value" @change="handleChange($event.target.value, 0, true)">
        <option v-for="num in 60" :value="num">{{ num }}</option>
    </select>
	  </div>
	  <div>
		<label for="hours">Hours:</label>
		<select value="selectedHours" @change="handleChange($event.target.value, 1, true)">
		<option v-for="num in 23" :value="num">{{ num }}</option>
	</select>
	  </div>
	  <div>
		<label for="days">Days:</label>
		<select value="selectedDays" @change="handleChange($event.target.value, 2, true)">
		<option v-for="num in 23" :value="num">{{ num }}</option>
	</select>
	  </div>
	</div>
	<button @click="addTimeAndClearSelections">Add Time and Clear Selections</button>
  </template>

<script>
export default {
	props: {
		value: {
			type: Array,
			default: ["0"],
		}
	},
  data() {
    return {
      selectedMinutes: null,
      selectedHours: null,
      selectedDays: null,
      totalTime: 0,
	  tempString: "",
	  formatedTimes: "",
	  updatedProps: [],
    };
  },
  emits: ['input'],
	setup(props, { emit }) {
		return { handleChange };

		function handleChange(value, index, update) {
			if (update == true) {
				if (props.value === null) {
					props.value = [];
				}
				props.value[index] = value;
			}
			emit('input', props.value);
		}
  },
  methods: {
	displayTime() {
		var returnTimeString = "";
			for (var i = 0; i < this.formatedTimes.length; i++) { // Loops over times and converts them to updated format
				if (i == this.formatedTimes.length - 1 && this.formatedTimes.length > 1) { // Adds "to" between 2nd and 3rd time
					returnTimeString += " to ";
				}
 				
				returnTimeString += this.formatedTimes[i]; // Converts time to updated format
				
				if (i != this.formatedTimes.length - 1 && this.formatedTimes.length > 2) { // Adds ", " between 1st and 2nd time
					returnTimeString += ", ";
				}
			}
		return returnTimeString;
	},
	calculateTime() {
		var totalTime = 0;
		console.log(parseFloat(this.$props.value[0]));
		if (parseFloat(this.$props.value[0]) !== null) {
			totalTime += parseFloat(this.$props.value[0]) / 60;
			console.log(this.$props.value[0]);
		}
		if (parseFloat(this.$props.value[1]) !== null) {
			totalTime += parseFloat(this.$props.value[1]);
			console.log(this.$props.value[1]);
		}
		console.log(totalTime);
		if (parseFloat(this.$props.value[2]) !== null) {
			totalTime += parseFloat(this.$props.value[2]) * 24;
			console.log(this.$props.value[2]);
		}
		if (totalTime === 0) {
			console.log(":(");
			return "0";
		}
		console.log(totalTime);
		return convertTime(totalTime);
		
	},
	addTimeAndClearSelections() {
        this.formatedTimes = this.calculateTime();

        this.selectedMinutes = null;
        this.selectedHours = null;
        this.selectedDays = null;
		//this.value = null;
		
    }
  },
};

function convertTime(timeToConvert) {
	
	//if (isNaN(timeToConvert)) {
	//	return "Invalid Input";
	//}

	var returnString = "";
	var dayFlag = false;
	// Days
	if (timeToConvert >= 48) {
		dayFlag = true;
		returnString = Math.floor(timeToConvert/24) + " days";
		timeToConvert = timeToConvert % 24; // add .floor() if minutes arent wanted WITH DAYS
		if (timeToConvert > 0) { // Adds space if days AND hours
			returnString += " ";
		}
	} else if (timeToConvert >= 24) {
		dayFlag = true;
		returnString = Math.floor(timeToConvert/24) + " day";
		timeToConvert = Math.floor(timeToConvert % 24);
		if (timeToConvert > 0) {
			returnString += " ";
		}
	} // to display days and no hours, change below if to else if
	if (timeToConvert > 0 || dayFlag) {
		// Hours
		var hourFlag = false;
		if (timeToConvert >= 1 && timeToConvert < 2) {
			returnString += Math.floor(timeToConvert) + " h";
			hourFlag = true;
		} else if (timeToConvert >= 2) {
			returnString += Math.floor(timeToConvert) + " hrs";
			hourFlag = true;
		}

		// Minutes
		var mins = (timeToConvert - Math.floor(timeToConvert)) * 60;
		if (mins == 1) {
			if (hourFlag) {
				returnString += " ";
			}
			returnString += Math.round(mins) + " min";
		} else if (mins > 1) {
			if (hourFlag) {
				returnString += " ";
			}
			returnString += Math.round(mins) + " mins";
		}
	} else {
		// catch all if negative input 
		returnString = "Negative Input";
	}
	return returnString;
}
</script>
