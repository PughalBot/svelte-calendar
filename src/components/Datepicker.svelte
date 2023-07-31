<script lang="ts">
  import { onMount } from 'svelte';
  import { getDaysInMonth, eachDayOfInterval, startOfMonth, endOfMonth, format, addDays } from 'date-fns';

  let selectedDate = new Date();
  let currentMonth = new Date().getMonth();
  let currentYear = new Date().getFullYear();
  let days = [];
  let months = Array.from({length: 12}, (_, i) => format(new Date(currentYear, i, 1), 'MMMM'));
  let years = Array.from({length: 11}, (_, i) => currentYear - 5 + i);
  let weekdays = ['Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun', 'Mon'];

  const loadDays = () => {
    const start = startOfMonth(new Date(currentYear, currentMonth));
    const end = endOfMonth(new Date(currentYear, currentMonth));
    days = eachDayOfInterval({ start, end });

    // Re-arrange days to start from Tuesday
    while (format(days[0], 'iii') !== 'Tue') {
      days.push(days.shift());
    }

    // Check if the selected date is still within the bounds of the current month
    if (selectedDate < start || selectedDate > end) {
      selectedDate = start;
    }
  };

  onMount(loadDays);

  let selectedDay = null;

  const selectDate = (date: Date) => {
    selectedDate = date;
    selectedDay = format(date, 'dd');
  };

  const nextMonth = () => {
    if (currentMonth === 11) {
      currentMonth = 0;
      currentYear++;
    } else {
      currentMonth++;
    }
    loadDays();
  };

  const prevMonth = () => {
    if (currentMonth === 0) {
      currentMonth = 11;
      currentYear--;
    } else {
      currentMonth--;
    }
    loadDays();
  };

  const changeMonth = (event: Event) => {
    currentMonth = months.indexOf((event.target as HTMLSelectElement).value);
    loadDays();
  };

  const changeYear = (event: Event) => {
    currentYear = parseInt((event.target as HTMLSelectElement).value);
    loadDays();
  };
</script>

<div class="container">
  <div class="controls">
    <button class="control-btn" on:click={prevMonth}>Prev</button>
    <select value={months[currentMonth]} on:change={changeMonth}>
      {#each months as month}
        <option>{month}</option>
      {/each}
    </select>    
    <select bind:value={currentYear} on:change={changeYear}>
      {#each years as year}
        <option>{year}</option>
      {/each}
    </select>
    <button class="control-btn" on:click={nextMonth}>Next</button>
  </div>

  <div class="weekdays">
    {#each weekdays as day}
      <div class="weekday">{day}</div>
    {/each}
  </div>

  <div class="days">
    {#each days as day (day)}
      <button
        class="day-btn {selectedDay === format(day, 'dd') ? 'selected' : ''}"
        on:click={() => selectDate(day)}
      >
        {format(day, 'dd')}
      </button>
    {/each}
  </div>

  <div class="selected-date">
    {format(selectedDate, 'dd/MM/yyyy')}
  </div>

</div>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 300px;
  margin: 0 auto;
  padding: 20px;
  border: 3px solid #d40909;
  border-radius: 25px;
  background: #000000;
  margin-top: 100px;
}


.controls {
  display: flex;
  justify-content: space-between;
  width: 100%;
  margin-bottom: 20px;
}

.control-btn {
  padding: 10px;
  border: none;
  border-radius: 5px;
  background: #dc2626;
  color: white;
  cursor: pointer;
}

.control-btn:hover {
  background: #7f1d1d;
}

.weekdays {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 10px;
  width: 100%;
}

.weekday {
  text-align: center;
  margin-bottom: 10px;
  color:#ffffff;
}

.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 10px;
  width: 100%;
}

.day-btn {
  padding: 5px;
  border: 2px solid ;
  border-radius: 5px;
  background: #ffffff;
  color: #dc2626;
  cursor: pointer;
}

.day-btn:hover {
  background: #7f1d1d;
}

.day-btn.selected {
    background-color: #7f1d1d;
    color: #ffffff;
  }

.selected-date {
  margin-top: 20px;
  background-color: none;
  border:2px solid #dc2626;
  color:#dc2626;
  box-shadow: 0 0 5px #dc2626;
  padding: 10px;
  border-radius: 5px;
  font-weight: bold;
  font-size: 18px;
  font-family: "Lucida Console", "Courier New", monospace;
}

select {
    border: 2px solid #ffffff; 
    color: #dc2626; 
    background-color: #000000; 
    padding: 5px; 
    border-radius: 5px; 
    cursor: pointer;
    width: 90px;
  }

  
  select:hover {
    border-color: #7f1d1d; 
  }

  
  select:focus {
    outline: none; 
    box-shadow: 0 0 5px #dc2626; 
  }

  
  option {
    background-color: #000000; 
    color: #dc2626; 
  }
</style>