<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Expense and Budget Tracker</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
/* Reset & base */
*{margin:0;padding:0;box-sizing:border-box;font-family:'Poppins',sans-serif;}
body{background:#d5e6f8;color:#333;line-height:1.6;font-size:16px;}
main{max-width:900px;margin:0 auto;padding:10px;}

/* Smooth jump */
section{padding:40px 15px;transition:transform 0.4s ease;scroll-margin-top:80px;}

/* Navigation */
nav{
  display:flex;justify-content:space-between;align-items:center;
  padding:15px 20px;background:#0e3574;color:rgb(241, 221, 221);position:sticky;top:0;z-index:1000;
  box-shadow:0 4px 12px rgba(0,0,0,0.1);border-radius:0 0 15px 15px;
}
nav h2{font-size:1.5em;color:white;}
nav ul{display:flex;list-style:none;gap:12px;transition:max-height 0.3s;}
nav ul li a{color:white;text-decoration:none;padding:10px 15px;border-radius:12px;transition:0.3s;font-weight:500;}
nav ul li a.active,nav ul li a:hover{background:#a5d8ff;color:#003566;}
.hamburger{display:none;flex-direction:column;cursor:pointer;position:absolute;top:18px;right:20px;z-index:1100;}
.hamburger div{width:28px;height:3px;background:white;margin:4px 0;transition:0.3s;}

/* Cards */
.card{
  background:#ffffff;padding:20px;margin:15px 0;border-radius:18px;
  box-shadow:0 6px 20px rgba(0,0,0,0.08);transition:transform 0.3s;
}
.card:hover{transform:translateY(-4px);}

/* Forms */
form input,form select,form button{
  display:block;width:100%;margin:12px 0;padding:14px;border-radius:12px;border:1px solid #d1d5db;font-size:1em;
}
form button{background:#0f397c;color:rgb(247, 229, 229);font-weight:bold;cursor:pointer;transition:0.3s;}
form button:hover{background:#5fa0ff;}

/* Lists */
#expense-list li,#recent-expenses li{
  display:flex;justify-content:space-between;align-items:center;
  background:#f8fafc;padding:12px;margin:8px 0;border-radius:12px;box-shadow:0 2px 6px rgba(0,0,0,0.05);flex-wrap:wrap;
}
.delete-btn{background:#f07979;color:rgb(245, 187, 187);border:none;padding:6px 12px;border-radius:10px;cursor:pointer;}
.delete-btn:hover{background:#ac1414;}

/* Charts */
.chart-container{background:#f8fafc;padding:20px;border-radius:18px;margin:15px 0;box-shadow:0 6px 20px rgba(0,0,0,0.08);}
canvas{max-width:100%;height:300px!important;}

/* Badges */
.badge{display:inline-block;background:#cce5ff;color:#003566;padding:6px 12px;border-radius:16px;margin:5px;font-weight:600;}

/* Progress bars */
.progress-bar{background:#e2e8f0;border-radius:16px;overflow:hidden;height:24px;margin:10px 0;}
.progress{background:#1f427a;height:100%;width:0%;transition:0.5s;}

/* Confetti */
.confetti{position:fixed;width:12px;height:12px;z-index:999;animation:fall 3s linear forwards;}
@keyframes fall{0%{top:-10px}100%{top:100vh;transform:rotate(720deg);}}

/* Responsive */
@media(max-width:768px){
  nav ul{flex-direction:column;max-height:0;overflow:hidden;width:90%;background:#3a86ff;border-radius:12px;position:absolute;top:60px;right:5%;box-shadow:0 4px 12px rgba(0,0,0,0.15);}
  nav ul.show{max-height:400px;}
  .hamburger{display:flex;}
  main{padding:10px;}
  form input,form select,form button{padding:12px;}
}
</style>
</head>
<body>

<nav>
  <h2 style="font-family:'Times New Roman'">Expense And Budget Tracker</h2>
  <div class="hamburger" onclick="toggleMenu()"><div></div><div></div><div></div></div>
  <ul id="nav-links">
    <li><a onclick="jumpTo('dashboard')">Dashboard</a></li>
    <li><a onclick="jumpTo('add-expense')">Add Expense</a></li>
    <li><a onclick="jumpTo('reports')">Reports</a></li>
    <li><a onclick="jumpTo('emotions')">Emotions</a></li>
    <li><a onclick="jumpTo('settings')">Settings</a></li>
  </ul>
</nav>
<section id="dashboard">
  <h3>📊 Dashboard Overview</h3>
  <div class="dashboard-grid">
    <div class="card"><b>Total Budget:</b> ₹<span id="total-budget">0</span></div>
    <div class="card"><b>Total Expense:</b> ₹<span id="total-expense">0</span></div>
    <div class="card">
      <b>Remaining Budget:</b> 
      <span id="remaining-budget">0</span>
    </div>
    <div class="card">
      <b>Goal Progress:</b>
      <div class="progress-bar"><div id="goal-progress-bar" class="progress"></div></div>
      <small id="goal-progress-text"></small>
    </div>
    <div class="card">
      <b>Level Progress:</b>
      <div class="progress-bar"><div id="xp-progress" class="progress"></div></div>
      <small>Level <span id="level">1</span> | XP <span id="xp">0</span></small>
    </div>
    <div class="card"><b>Saving Streak:</b> <span id="streak">0 days</span></div>
  </div>
  
  <h4>⭐ Your Badges</h4>
  <div id="badges" class="card">No badges yet.</div>

  <h4>📝 Recent Expenses</h4>
  <ul id="recent-expenses"></ul>
</section>
/* --- CSS CHANGES --- */
<style>
/* Reset & base */
*{margin:0;padding:0;box-sizing:border-box;font-family:'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";}
body{background:#f0f4f8; /* Lighter background */
  color:#1f2937; /* Darker text for contrast */
  line-height:1.6;font-size:16px;}
main{max-width:1000px;margin:20px auto;padding:10px;}

/* Smooth jump */
section{padding:30px 15px;scroll-margin-top:70px;}

/* Navigation */
nav{
  display:flex;justify-content:space-between;align-items:center;
  padding:15px 30px;background:#ffffff; /* White navbar */
  color:#1f2937;position:sticky;top:0;z-index:1000;
  box-shadow:0 10px 30px rgba(0,0,0,0.05);border-radius:0 0 20px 20px;
}
nav h2{font-size:1.6em;color:#1f2937;}
nav ul{display:flex;list-style:none;gap:15px;transition:max-height 0.3s;}
nav ul li a{color:#4b5563;text-decoration:none;padding:10px 18px;border-radius:12px;transition:0.3s;font-weight:600;}
nav ul li a.active,nav ul li a:hover{background:#3b82f6; /* New primary accent color */
  color:white;
}
.hamburger{display:none;flex-direction:column;cursor:pointer;position:absolute;top:18px;right:20px;z-index:1100;}
.hamburger div{width:28px;height:3px;background:#1f2937;margin:4px 0;transition:0.3s;}

/* Cards */
.card{
  background:#ffffff;padding:25px;margin:15px 0;border-radius:20px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1); /* Subtle, modern shadow */
  transition:transform 0.3s, box-shadow 0.3s;
  display: flex; /* Ensure inner elements are flexible */
  flex-direction: column;
}
.card:hover{transform:translateY(-2px);
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -4px rgba(0, 0, 0, 0.1);
}
.card b{font-size: 1.1em; color: #1f2937;}
.card span, .card b {margin-top: 5px; font-size: 1.5em; font-weight: 700; color: #3b82f6;} /* Highlight key numbers */
#remaining-budget.negative { color: #ef4444; } /* Red for negative remaining budget */
.dashboard-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 15px;
}
/* Forms */
form input,form select,form button{
  display:block;width:100%;margin:15px 0;padding:16px;border-radius:12px;border:1px solid #d1d5db;font-size:1em;
  background: #f9fafb; /* Light input background */
}
form button{background:#3b82f6; /* Primary button color */
  color:rgb(255, 255, 255);font-weight:bold;cursor:pointer;transition:0.3s;
  box-shadow: 0 4px 10px rgba(59, 130, 246, 0.3);
}
form button:hover{background:#2563eb;}

/* Lists */
#expense-list li,#recent-expenses li{
  display:flex;justify-content:space-between;align-items:center;
  background:#ffffff; /* White list background */
  padding:15px;margin:10px 0;border-radius:12px;box-shadow:0 1px 3px rgba(0,0,0,0.05);flex-wrap:wrap;
  border-left: 5px solid #3b82f6; /* Subtle accent */
}
.delete-btn{background:#ef4444;color:white;border:none;padding:8px 15px;border-radius:10px;cursor:pointer;font-weight:600;}
.delete-btn:hover{background:#dc2626;}

/* Charts */
.chart-container{background:#ffffff;padding:25px;border-radius:20px;margin:15px 0;box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);}

/* Badges */
.badge{display:inline-block;background:#dbeafe; /* Light blue badge background */
  color:#1e40af;padding:8px 16px;border-radius:20px;margin:5px;font-weight:700;font-size:0.9em;
}

/* Progress bars */
.progress-bar{background:#e5e7eb;border-radius:16px;overflow:hidden;height:20px;margin:10px 0;}
.progress{background:#3b82f6;height:100%;width:0%;transition:0.5s;}
#xp-progress { background: #10b981; } /* Different color for XP bar */

/* Confetti - keep as is */
/* Responsive - adjust colors/styles */
@media(max-width:768px){
  nav ul{flex-direction:column;max-height:0;overflow:hidden;width:90%;background:#ffffff; /* White dropdown */
    border-radius:12px;position:absolute;top:65px;right:5%;box-shadow:0 10px 20px rgba(0,0,0,0.15);border: 1px solid #e5e7eb;}
  nav ul.show{max-height:400px;}
  .hamburger div { background: #1f2937; }
  .hamburger{display:flex;}
  main{padding:10px;}
  form input,form select,form button{padding:14px;}
}
</style>
/* --- JAVASCRIPT CHANGES (inside updateDashboard) --- */
function updateDashboard(){
  let total=expenses.reduce((a,b)=>a+b.amount,0);
  let remaining = totalBudget - total; // Calculate remaining

  document.getElementById('total-budget').textContent=totalBudget.toLocaleString('en-IN');
  document.getElementById('total-expense').textContent=total.toLocaleString('en-IN');
  
  // Update remaining budget text and apply negative class if needed
  let remainingEl = document.getElementById('remaining-budget');
  remainingEl.textContent=remaining.toLocaleString('en-IN');
  if (remaining < 0) {
    remainingEl.classList.add('negative');
    remainingEl.innerHTML = `(${remainingEl.textContent.replace('-', '-₹')})`; // Display as negative amount
  } else {
    remainingEl.classList.remove('negative');
    remainingEl.innerHTML = `₹${remainingEl.textContent}`;
  }


  let progress=Math.min((total/goalAmount)*100,100);
  document.getElementById('goal-progress-bar').style.width=progress+'%';
  document.getElementById('goal-progress-text').textContent=`₹${total.toLocaleString('en-IN')} / ₹${goalAmount.toLocaleString('en-IN')} (${progress.toFixed(1)}%)`;
  
  // Streak logic (Keep existing logic)
  let today=new Date().toISOString().split('T')[0];
  if(lastDate===today){} else if(lastDate===new Date(Date.now()-86400000).toISOString().split('T')[0]){streakDays++;awardBadge('Streak Master');} else{streakDays=1;}
  lastDate=today; 
  
  document.getElementById('streak').textContent=streakDays+' days';
  document.getElementById('xp').textContent=xp; 
  document.getElementById('level').textContent=level;
  
  // XP progress calculation
  const xpNeededForNextLevel = level * 50;
  document.getElementById('xp-progress').style.width=Math.min((xp/xpNeededForNextLevel)*100,100)+'%';
  // Update the XP text to show progress towards next level
  document.querySelector('#dashboard .card:nth-child(5) small').innerHTML = `Level ${level} | **XP ${xp}** / ${xpNeededForNextLevel}`;

  checkGoal(total); displayBadges(); saveData();
}

// Initial active link setting after page load
document.addEventListener('DOMContentLoaded', () => {
    // Add active class to the first link on page load
    document.querySelector('nav ul li a').classList.add('active');
});

// Update displayBadges to show a message when empty
function displayBadges(){
  let container=document.getElementById('badges'); 
  container.innerHTML=''; 
  if (badges.length === 0) {
    container.textContent = 'No badges yet. Keep tracking your expenses to earn rewards!';
    container.style.justifyContent = 'center';
  } else {
    badges.forEach(b=>{let span=document.createElement('span');span.className='badge';span.textContent=b;container.appendChild(span);});
  }
}
