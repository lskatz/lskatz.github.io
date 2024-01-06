---
layout: default
title: Connections
permalink: /connections/
---

# Connections

<style>
  #message {
    margin:5px;
  }
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    min-height: 100vh;
  }

  .interactive-container {
    border: 2px solid #ddd; /* Slightly heavier border */
    padding: 10px; /* Add padding for better visual appearance */
    margin-bottom: 20px;
  }

  .interactive-table {
    border-collapse: collapse;
    margin: 0 auto; /* Center the table within the container */
  }

  .interactive-cell {
    width: 75px;
    height: 75px;
    border: 1px solid #ddd;
    cursor: pointer;
  }

  .colored {
    background-color: #f2f2f2; /* Light grey color */
  }

  .yellow {
    background-color: yellow;
  }

  .green {
    background-color: green;
  }

  .blue {
    background-color: blue;
  }

  .purple {
    background-color: purple;
  }

  .button-container {
    text-align: center;
    margin-top: 10px;
  }

	.success-message {
		background-color: #4CAF50;
		color: white;
	}

	.error-message {
		background-color: #f44336;
		color: white;
	}

</style>

<!-- Include js-yaml library for YAML parsing -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/js-yaml/4.0.0/js-yaml.min.js"></script>

<script>
  document.addEventListener('DOMContentLoaded', function () {
    // Function to populate the table with data from YAML
    function populateTable(data) {
      var table = document.querySelector('.interactive-table');
      table.innerHTML = ''; // Clear existing table content

      var categories = Object.keys(data);

      categories.forEach(function (category, rowIndex) {
        var categoryData = data[category];
        var row = table.insertRow(rowIndex);

        var members = categoryData.members;
        var color = categoryData.color;

        members.forEach(function (value, cellIndex) {
          var cell = row.insertCell(cellIndex);
          cell.classList.add('interactive-cell');
          cell.textContent = value;

          // Add data-category attribute to each cell
          cell.setAttribute('data-category', category);
        });
      });

      // Call the function to randomize the table after populating it
      randomizeTable();
    }


// Function to update the message div
function updateMessage(message, isSuccess) {
  var messageDiv = document.getElementById('message');
  
  // Clear previous styles
  messageDiv.className = '';

  // Set new content and styles
  messageDiv.textContent = message;

  if (isSuccess) {
    // Apply success class
    messageDiv.classList.add('success-message');
  } else {
    // Apply error class
    messageDiv.classList.add('error-message');
  }
}


    // Function to randomize the locations of all cells in the table while retaining four rows of four columns
    function randomizeTable() {
      var table = document.querySelector('.interactive-table');
      var cells = Array.from(table.querySelectorAll('.interactive-cell'));
      var shuffledCells = shuffleArray(cells);

      // Clear existing content in the table
      table.innerHTML = '';

      // Reassign cells to rows and columns
      for (var i = 0; i < 4; i++) {
        var row = table.insertRow(i);
        for (var j = 0; j < 4; j++) {
          var originalCell = shuffledCells[i * 4 + j];
          var cell = originalCell.cloneNode(true); // Clone the cell
          row.appendChild(cell);
        }
      }

      // Add click event listeners for cell coloring
      addCellClickListeners();
    }

    // Function to add click event listeners for cell coloring
    function addCellClickListeners() {
      var cells = document.querySelectorAll('.interactive-cell');

      cells.forEach(function (cell) {
        cell.addEventListener('click', function () {
          if (!cell.classList.contains('yellow') && !cell.classList.contains('green') &&
              !cell.classList.contains('blue') && !cell.classList.contains('purple')) {
            if (cell.classList.contains('colored')) {
              cell.classList.remove('colored');
            } else {
              cell.classList.add('colored');
            }
          }
        });
      });
    }

    // Function to verify selected cells and move them to the top of the table
    function verifyAndMoveCells() {
      var selectedCells = document.querySelectorAll('.interactive-cell.colored');

      if (selectedCells.length !== 4) {
        updateMessage('Please select exactly four cells.');
        return;
      }

      // Extract category name from the first selected cell
      var categoryName = getCategoryName(selectedCells[0]);

      // Check if all selected cells belong to the same category
      var isSameCategory = Array.from(selectedCells).every(function (cell) {
        return getCategoryName(cell) === categoryName;
      });

      if (isSameCategory) {
        updateMessage('Got the ' + categoryName + ' category');

        // Create a new row in table "solution"
        var solutionTable = document.getElementById('solution');
        var solutionRow = solutionTable.insertRow();

        // Clone every cell that is selected to the new row and delete all cloned cells from the original table
        selectedCells.forEach(function (selectedCell) {
          var clonedCell = selectedCell.cloneNode(true);
          solutionRow.appendChild(clonedCell);
          //selectedCell.parentNode.removeChild(selectedCell);
          selectedCell.textContent = "";
          clonedCell.classList.remove('colored');
        });

        // Shift all blank cells such that there are still four columns per row
        //shiftBlankCells();

        // There should be one blank row which should be deleted
        deleteBlankRow();
      } else {
        updateMessage('Incorrect');
      }

      // Deselect all selected cells
      selectedCells.forEach(function (selectedCell) {
        selectedCell.classList.remove('colored');
      });
    }
// Helper function to shift all blank cells
function shiftBlankCells() {
  var table = document.querySelector('.interactive-table');
  var rows = table.getElementsByTagName('tr');
  var nonBlankCells = [];

  // Extract all non-blank cells and remove them from the table
  for (var i = 0; i < rows.length; i++) {
    var cells = rows[i].getElementsByTagName('td');
    for (var j = 0; j < cells.length; j++) {
      if (cells[j].textContent !== '') {
        nonBlankCells.push(cells[j].cloneNode(true));
        cells[j].parentNode.removeChild(cells[j]);
      }
    }
  }

  // Clear the table
  table.innerHTML = '';

  // Remake the table with all non-blank cells
  for (var k = 0; k < nonBlankCells.length; k++) {
    var rowIndex = Math.floor(k / 4);
    if (!table.rows[rowIndex]) {
      table.insertRow(rowIndex);
    }

    var cellIndex = k % 4;
    table.rows[rowIndex].appendChild(nonBlankCells[k]);
  }
}

    // Helper function to delete the first blank row
    function deleteBlankRow() {
      var table = document.querySelector('.interactive-table');
      var firstRow = table.querySelector('tr:empty');

      if (firstRow) {
        firstRow.parentNode.removeChild(firstRow);
      }
    }

    // Helper function to get the category name from a cell
    function getCategoryName(cell) {
      // Assuming the category name is stored as a data attribute (data-category)
      return cell.getAttribute('data-category');
    }


    // Helper function to shuffle a copy of an array using the Fisher-Yates algorithm
    function shuffleArray(array) {
      var shuffledArray = array.slice(); // Create a copy of the array
      for (var i = shuffledArray.length - 1; i > 0; i--) {
        var j = Math.floor(Math.random() * (i + 1));
        [shuffledArray[i], shuffledArray[j]] = [shuffledArray[j], shuffledArray[i]];
      }
      return shuffledArray;
    }

    // Add click event listener for the "Submit" button
    var submitButton = document.getElementById('submitButton');
    submitButton.addEventListener('click', function () {
      // Specify the action for the "Submit" button later
      verifyAndMoveCells();
    });

    // Add click event listener for the "Deselect All" button
    var deselectAllButton = document.getElementById('deselectAllButton');
    deselectAllButton.addEventListener('click', function () {
      var coloredCells = document.querySelectorAll('.interactive-cell.colored');
      coloredCells.forEach(function (coloredCell) {
        coloredCell.classList.remove('colored');
      });
    });

    // Add click event listener for the "Randomize" button
    var randomizeButton = document.getElementById('randomizeButton');
    randomizeButton.addEventListener('click', function () {
      randomizeTable();
    });

    // Sample YAML data
    var defaultYaml = {
      numbers: {
        members: [1, 2, 3, 4],
        color: 'yellow'
      },
      letters: {
        members: ['a', 'b', 'c', 'd'],
        color: 'green'
      },
      ten: {
        members: ['x', 10, 'ten', 1010],
        color: 'blue'
      },
      'nytimes games': {
        members: ['connections', 'sudoku', 'mini', 'wordle'],
        color: 'purple'
      }
    };

    // Function to retrieve Base64-encoded YAML from the URL
    function getBase64YamlFromUrl() {
      var urlParams = new URLSearchParams(window.location.search);
      var base64Yaml = urlParams.get('yaml');
      return base64Yaml;
    }

    // Function to decode Base64 and parse YAML
    function decodeBase64Yaml(encodedYaml) {
      try {
        var decodedString = atob(encodedYaml);
        var decodedYaml = jsyaml.safeLoad(decodedString);
        return decodedYaml;
      } catch (error) {
        console.error('Error decoding Base64 YAML:', error);
        return null;
      }
    }

    // Use the decoded YAML data or default YAML if not present
    var base64Yaml = getBase64YamlFromUrl();
    var yamlData = base64Yaml ? decodeBase64Yaml(base64Yaml) : defaultYaml;

    // Call the function to populate the table
    populateTable(yamlData);
  });
</script>



<div class="container">
  <div id="message">Select four groups of four!</div>
  <div class="interactive-container">
    <table class="interactive-table"></table>
  </div>
  <div class="button-container">
    <button id="submitButton">Submit</button>
    <button id="deselectAllButton">Deselect All</button>
    <button id="randomizeButton">Randomize</button>
  </div>
  <div style="margin-top:1em">
    <table id="solution">
  </table>
</div>
