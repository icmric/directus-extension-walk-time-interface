<template>
	<p>Total Time: {{ displayTime() }}</p>
	<div style="display: flex; justify-content: space-around;">
	  <div>
		<label for="minutes">Minutes:</label>
		<select :value="selectedMinutes" @change="handleChange($event.target.value, 0, true)">
        <option v-for="num in 60" :value="num">{{ num }}</option>
    </select>
	  </div>
	  <div>
		<label for="hours">Hours:</label>
		<select :value="selectedHours" @change="handleChange($event.target.value, 1, true)">
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
	<button @click="addTime">Add Time</button>
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
	  formatedTimes: [],
	  draggedTile: null,
	  localHoursArray: [],
    };
  },
  emits: ['input'],
  methods: {
	handleChange(value, index) {
		if (index == 0) {
			this.selectedMinutes = value;
		} else if (index == 1) {
			this.selectedHours = value;
		} else if (index == 2) {
			this.selectedDays = value;
		}
	},
	displayTime() {
		// loads, calculates and renders values when a save is loaded
		if (this.localHoursArray.length == 0 && this.$props.value != null) { 
			this.localHoursArray = []; 
			Object.values(this.$props.value).forEach(element => {
				this.localHoursArray.push(element);
				this.formatedTimes.push(convertTime(element));
			})
		}
		//this.formatedTimes = [];
		this.localHoursArray.sort((a,b) => a - b); 

		var returnTimeString = "";
			for (var i = 0; i < this.localHoursArray.length; i++) { // Loops over times and converts them to updated format
				if (i == this.localHoursArray.length - 1 && this.localHoursArray.length > 1) { // Adds "to" between 2nd and 3rd time
					returnTimeString += " to ";
				} else if (this.localHoursArray.length > 2 && i != 0) {
					returnTimeString += ", "; // Adds ", " between 1st and 2nd time
				}
				
				returnTimeString += convertTime(this.localHoursArray[i]); // Converts time to updated format
			}
		return returnTimeString;
		
	},
	calculateTime() {
		var totalTime = 0;
		if (this.selectedMinutes != null) {
			totalTime += parseFloat(this.selectedMinutes) / 60;
		}
		if (this.selectedHours != null) {
			totalTime += parseFloat(this.selectedHours);
		}
		if (this.selectedDays != null) {
			totalTime += parseFloat(this.selectedDays) * 24;
		}
		if (totalTime == 0) {
			return "0";
		}
		this.localHoursArray.push(totalTime);
		return totalTime;
		
	},
	addTime() {
        this.calculateTime();
		var json = { ...this.localHoursArray };
		this.$emit('input', json);
        this.clearSelections();
		
    },
	clearSelections() {
		this.selectedMinutes = null;
		this.selectedHours = null;
		this.selectedDays = null;
	},
  },
};

function convertTime(timeToConvert) {
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
