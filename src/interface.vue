<template>
	<p>Total Time: {{ displayTime() }}</p>
	<div style="display: flex; justify-content: space-around;">
	  <div>
		<label for="minutes">Minutes:</label>
		<input list="minutesOptions" v-model="selectedMinutes" id="minutes" @input="calculateTime()">
		<datalist id="minutesOptions">
		  <option v-for="min in minutesOptions" :key="min" :value="min">{{ min }}</option>
		</datalist>
	  </div>
	  <div>
		<label for="hours">Hours:</label>
		<input list="hoursOptions" v-model="selectedHours" id="hours" @input="calculateTime()">
		<datalist id="hoursOptions">
		  <option v-for="hour in hoursOptions" :key="hour" :value="hour">{{ hour }}</option>
		</datalist>
	  </div>
	  <div>
		<label for="days">Days:</label>
		<input list="daysOptions" v-model="selectedDays" id="days" @input="calculateTime()">
		<datalist id="daysOptions">
		  <option v-for="day in daysOptions" :key="day" :value="day">{{ day }}</option>
		</datalist>
	  </div>
	</div>
	<button @click="addTimeAndClearSelections">Add Time and Clear Selections</button>
  </template>

<script>
export default {
  data() {
    return {
      selectedMinutes: null,
      selectedHours: null,
      selectedDays: null,
      minutesOptions: [0, 1, 5, 10, 15, 30, 60],
      hoursOptions: [...Array(24).keys()].map(hour => hour.toString()),
      daysOptions: [...Array(14).keys()].map(day => day.toString()), // Convert to strings
      totalTime: 0,
	  tempString: "",
	  formatedTimes: [],
	  updatedProps: [],
    };
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
		if (parseFloat(this.selectedDays) !== NaN && this.selectedDays !== null) {
			totalTime += parseFloat(this.selectedDays) * 24;
		}
		if (parseFloat(this.selectedHours) !== NaN && this.selectedHours !== null) {
			totalTime += parseFloat(this.selectedHours);
		}
		if (parseFloat(this.selectedMinutes) !== NaN && this.selectedMinutes !== null) {
			totalTime += parseFloat(this.selectedMinutes) / 60;
		}
		if (totalTime === 0) {
			return "0";
		}
		return convertTime(totalTime);
		
	},
	addTimeAndClearSelections() {
        this.formatedTimes.push(this.calculateTime());
        this.selectedMinutes = null;
        this.selectedHours = null;
        this.selectedDays = null;
		
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
		timeToConvert = timeToConvert % 24; // remove .floor() if minutes are wanted WITH DAYS
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
