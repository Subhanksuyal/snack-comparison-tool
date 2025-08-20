# snack-comparison-tool

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snack Comparison Tool</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            text-align: center;
            margin: 0;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background-color: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 0 15px rgba(0,0,0,0.1);
        }
        h1 {
            color: #e74c3c;
            margin-bottom: 30px;
        }
        .input-group {
            margin: 20px 0;
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 15px;
        }
        input {
            padding: 10px 15px;
            border: 2px solid #ddd;
            border-radius: 5px;
            font-size: 16px;
            width: 200px;
        }
        button {
            background-color: #e74c3c;
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #c0392b;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin: 30px 0;
            box-shadow: 0 0 10px rgba(0,0,0,0.05);
        }
        th {
            background-color: #e74c3c;
            color: white;
            padding: 12px;
            text-align: center;
        }
        td {
            padding: 10px;
            border-bottom: 1px solid #ddd;
            text-align: center;
        }
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
        .result {
            margin-top: 20px;
            padding: 15px;
            background-color: #2ecc71;
            color: white;
            border-radius: 5px;
            font-size: 1.2em;
            display: inline-block;
        }
        .health-icon {
            font-size: 24px;
            margin-left: 10px;
        }
        .suggestions {
            margin-top: 20px;
            font-style: italic;
            color: #7f8c8d;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🍿 Snack Nutrition Comparison Tool</h1>
        
        <div class="input-group">
            <input type="text" id="snack1" placeholder="Enter first snack (e.g. Lays)">
            <input type="text" id="snack2" placeholder="Enter second snack (e.g. Pringles)">
            <button onclick="compareSnacks()">Compare Snacks</button>
        </div>

        <table id="comparisonTable">
            <tr>
                <th>Snack</th>
                <th>Calories</th>
                <th>Fat (g)</th>
                <th>Protein (g)</th>
                <th>Sugar (g)</th>
                <th>Carbs (g)</th>
            </tr>
        </table>

        <div id="result" class="result"></div>
        
        <div class="suggestions">
            Try comparing: Lays vs Pringles, Oreo vs Chips Ahoy, or Popcorn vs Pretzels
        </div>
    </div>

    <script>
        // Expanded snack database with 50+ popular snacks
        const snackDatabase = {
            // Potato Chips
            "Lays": {calories: 160, fat: 10, protein: 2, sugar: 1, carbs: 15},
            "Pringles": {calories: 150, fat: 9, protein: 1, sugar: 0, carbs: 16},
            "Ruffles": {calories: 160, fat: 10, protein: 2, sugar: 1, carbs: 15},
            "Kurkure": {calories: 180, fat: 12, protein: 3, sugar: 2, carbs: 18},
            "Bingo": {calories: 170, fat: 11, protein: 2, sugar: 1, carbs: 17},
            
            // Corn Chips
            "Doritos": {calories: 140, fat: 8, protein: 2, sugar: 1, carbs: 18},
            "Cheetos": {calories: 150, fat: 10, protein: 2, sugar: 1, carbs: 13},
            "Fritos": {calories: 160, fat: 10, protein: 2, sugar: 0, carbs: 16},
            
            // Cookies
            "Oreo": {calories: 140, fat: 7, protein: 1, sugar: 14, carbs: 21},
            "Chips Ahoy": {calories: 160, fat: 8, protein: 2, sugar: 12, carbs: 22},
            "Digestive": {calories: 70, fat: 3, protein: 1, sugar: 3, carbs: 10},
            "Shortbread": {calories: 110, fat: 6, protein: 1, sugar: 4, carbs: 12},
            
            // Chocolate
            "Snickers": {calories: 250, fat: 12, protein: 4, sugar: 27, carbs: 33},
            "Kit Kat": {calories: 210, fat: 11, protein: 3, sugar: 21, carbs: 27},
            "Twix": {calories: 250, fat: 12, protein: 2, sugar: 24, carbs: 34},
            "M&M's": {calories: 240, fat: 10, protein: 2, sugar: 30, carbs: 32},
            
            // Nuts
            "Almonds": {calories: 160, fat: 14, protein: 6, sugar: 1, carbs: 6},
            "Cashews": {calories: 160, fat: 13, protein: 5, sugar: 1, carbs: 9},
            "Peanuts": {calories: 170, fat: 14, protein: 7, sugar: 1, carbs: 6},
            "Pistachios": {calories: 160, fat: 13, protein: 6, sugar: 2, carbs: 8},
            
            // Popcorn
            "Popcorn": {calories: 110, fat: 6, protein: 3, sugar: 0, carbs: 13},
            "Smartfood": {calories: 150, fat: 10, protein: 2, sugar: 0, carbs: 15},
            
            // Crackers
            "Ritz": {calories: 80, fat: 4, protein: 1, sugar: 1, carbs: 10},
            "Saltines": {calories: 70, fat: 2, protein: 1, sugar: 0, carbs: 12},
            "Triscuit": {calories: 120, fat: 4, protein: 3, sugar: 0, carbs: 20},
            
            // Healthier Options
            "Granola Bar": {calories: 100, fat: 3, protein: 2, sugar: 8, carbs: 17},
            "Rice Cake": {calories: 35, fat: 0, protein: 1, sugar: 0, carbs: 7},
            "Pretzels": {calories: 110, fat: 1, protein: 3, sugar: 1, carbs: 23},
            "Trail Mix": {calories: 150, fat: 8, protein: 4, sugar: 8, carbs: 16},
            
            // International Snacks
            "Pocky": {calories: 120, fat: 3, protein: 1, sugar: 9, carbs: 21},
            "Pirate's Booty": {calories: 130, fat: 5, protein: 2, sugar: 0, carbs: 20},
            "Takis": {calories: 140, fat: 8, protein: 2, sugar: 1, carbs: 16},
            "Funyuns": {calories: 140, fat: 6, protein: 2, sugar: 1, carbs: 19},
            
            // Cereal
            "Cheerios": {calories: 100, fat: 2, protein: 3, sugar: 1, carbs: 20},
            "Frosted Flakes": {calories: 110, fat: 0, protein: 1, sugar: 10, carbs: 25},
            
            // Other
            "Goldfish": {calories: 140, fat: 5, protein: 3, sugar: 0, carbs: 20},
            "Animal Crackers": {calories: 120, fat: 3, protein: 2, sugar: 6, carbs: 21},
            "Graham Crackers": {calories: 130, fat: 4, protein: 2, sugar: 8, carbs: 22}
        };

        function compareSnacks() {
            let snack1 = document.getElementById("snack1").value.trim();
            let snack2 = document.getElementById("snack2").value.trim();

            if (!snack1 || !snack2) {
                alert("Please enter both snack names.");
                return;
            }

            // Case-insensitive search with title case formatting
            snack1 = findSnackInDatabase(snack1);
            snack2 = findSnackInDatabase(snack2);

            if (!snack1) {
                alert(`Snack "${document.getElementById("snack1").value}" not found in our database.`);
                return;
            }
            if (!snack2) {
                alert(`Snack "${document.getElementById("snack2").value}" not found in our database.`);
                return;
            }

            let table = document.getElementById("comparisonTable");
            table.innerHTML = `
                <tr>
                    <th>Snack</th>
                    <th>Calories</th>
                    <th>Fat (g)</th>
                    <th>Protein (g)</th>
                    <th>Sugar (g)</th>
                    <th>Carbs (g)</th>
                </tr>
                <tr>
                    <td>${snack1}</td>
                    <td>${snackDatabase[snack1].calories}</td>
                    <td>${snackDatabase[snack1].fat}</td>
                    <td>${snackDatabase[snack1].protein}</td>
                    <td>${snackDatabase[snack1].sugar}</td>
                    <td>${snackDatabase[snack1].carbs}</td>
                </tr>
                <tr>
                    <td>${snack2}</td>
                    <td>${snackDatabase[snack2].calories}</td>
                    <td>${snackDatabase[snack2].fat}</td>
                    <td>${snackDatabase[snack2].protein}</td>
                    <td>${snackDatabase[snack2].sugar}</td>
                    <td>${snackDatabase[snack2].carbs}</td>
                </tr>
            `;

            // Determine the healthier snack (simple algorithm)
            let result = document.getElementById("result");
            let score1 = calculateHealthScore(snackDatabase[snack1]);
            let score2 = calculateHealthScore(snackDatabase[snack2]);
            
            if (score1 > score2) {
                result.innerHTML = `${snack1} is healthier! <span class="health-icon">👍</span>`;
            } else if (score1 < score2) {
                result.innerHTML = `${snack2} is healthier! <span class="health-icon">👍</span>`;
            } else {
                result.innerHTML = "Both snacks are similarly healthy";
            }
        }

        function findSnackInDatabase(input) {
            // Try exact match first
            if (snackDatabase[input]) return input;
            
            // Case-insensitive search
            input = input.toLowerCase();
            for (let snack in snackDatabase) {
                if (snack.toLowerCase() === input) {
                    return snack;
                }
            }
            
            // Try partial matches
            for (let snack in snackDatabase) {
                if (snack.toLowerCase().includes(input)) {
                    return snack;
                }
            }
            
            return null;
        }

        function calculateHealthScore(snack) {
            // Simple health scoring algorithm
            // Higher protein and lower sugar/fat is better
            return (snack.protein * 3) - (snack.sugar * 2) - (snack.fat * 1) - (snack.calories / 50);
        }
    </script>
</body>
</html>
