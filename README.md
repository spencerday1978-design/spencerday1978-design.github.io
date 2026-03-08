
<script>
    // --- APP DATA STATE ---
    let profile = JSON.parse(localStorage.getItem('fitness_profile')) || { weight: 80, goal: 75, calories: 2000, protein: 160 };
    let logs = JSON.parse(localStorage.getItem('fitness_logs')) || { meals: [], weights: [] };

    // --- INITIALIZE UI ---
    function updateUI() {
        // Update Today's Macros (Summary)
        const today = new Date().toLocaleDateString();
        const todaysMeals = logs.meals.filter(m => m.date === today);
        
        const totals = todaysMeals.reduce((acc, meal) => {
            acc.p += Number(meal.p);
            acc.cal += Number(meal.cal);
            return acc;
        }, { p: 0, cal: 0 });

        document.getElementById('macro-summary').innerHTML = `
            <div><small>Calories</small><br><strong>${totals.cal} / ${profile.calories}</strong></div>
            <div><small>Protein</small><br><strong>${totals.p}g / ${profile.protein}g</strong></div>
        `;
        
        generateAICoachFeedback(totals);
    }

    // --- AI COACH LOGIC (Adaptive) ---
    function generateAICoachFeedback(totals) {
        const feedbackBox = document.getElementById('ai-feedback');
        let message = "Keep tracking! Data helps me adjust your plan.";

        if (totals.p < profile.protein * 0.5 && totals.cal > 500) {
            message = "Protein is low. Focus on lean meat or Greek yogurt for your next meal.";
        } else if (totals.cal > profile.calories) {
            message = "You've hit your calorie ceiling. Switch to high-volume, low-cal snacks.";
        } else if (totals.p >= profile.protein) {
            message = "Protein target reached! Great work supporting muscle recovery.";
        }

        feedbackBox.innerText = `"${message}"`;
    }

    // --- LOGGING FUNCTIONS ---
    function saveMeal() {
        const meal = {
            name: document.getElementById('meal-name').value,
            cal: document.getElementById('meal-cal').value,
            p: document.getElementById('meal-p').value,
            date: new Date().toLocaleDateString()
        };

        if (!meal.name || !meal.cal) return alert("Enter meal details!");

        logs.meals.push(meal);
        localStorage.setItem('fitness_logs', JSON.stringify(logs));
        updateUI();
        
        // Clear inputs
        document.getElementById('meal-name').value = '';
        document.getElementById('meal-cal').value = '';
        document.getElementById('meal-p').value = '';
        alert("Meal saved locally!");
    }

    // --- PHOTO AI SIMULATION ---
    function simulatePhotoAI() {
        const btn = document.getElementById('photo-btn');
        btn.innerText = "Analyzing Photo...";
        
        setTimeout(() => {
            document.getElementById('meal-name').value = "Chicken & Avocado Salad";
            document.getElementById('meal-cal').value = 450;
            document.getElementById('meal-p').value = 35;
            btn.innerText = "📸 Photo Macro Estimate";
            alert("AI Estimate: ~450 Cal, 35g Protein");
        }, 1500);
    }

    // --- ADAPTIVE MACRO SUGGESTION ---
    function suggestAdjustment() {
        const weightTrend = -0.5; // In a real app, this calculates from logs.weights
        if (weightTrend > 0) {
            profile.calories -= 100;
            alert("Weight is trending up. Reducing daily target by 100 calories.");
        } else {
            alert("Trend is stable. No adjustment needed yet.");
        }
        localStorage.setItem('fitness_profile', JSON.stringify(profile));
        updateUI();
    }

    // Run on load
    window.onload = updateUI;
</script>
