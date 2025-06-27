<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>OSRS Skilling App</title>
  <style>
    body {
      background: #000;
      color: #FFF;
      font-family: 'Courier New', monospace;
      margin: 0;
      padding: 10px;
    }
    h1 {
      color: gold;
      font-size: 24px;
      text-align: center;
    }
    .skills, .bank, .tools, .log {
      border: 1px solid #555;
      padding: 10px;
      margin: 5px 0;
      background: #111;
    }
    button {
      font-size: 14px;
      padding: 5px;
      margin: 2px;
      background: #222;
      color: gold;
      border: 1px solid #555;
      border-radius: 4px;
    }
    button:hover {
      background: #333;
    }
    .log-line {
      font-size: 14px;
    }
    .log-success { color: limegreen; }
    .log-error { color: red; }
    .log-xp { color: yellow; }
  </style>
</head>
<body>
  <h1>OSRS Skilling + Tools + Efficiency</h1>

  <div class="skills" id="skills-display"></div>

  <div class="tools" id="tools-display"></div>

  <div id="buttons"></div>

  <div class="bank" id="bank-display">
    <strong>Bank:</strong> 
  </div>

  <div class="log" id="log" style="height: 150px; overflow-y: auto;">
    <em>Welcome to OSRS Skilling App!</em>
  </div>

  <script>
    const skillList = [
      'Mining', 'Fishing', 'Woodcutting',
      'Attack', 'Strength', 'Defence', 'Hitpoints', 'Ranged', 'Prayer', 'Magic',
      'Cooking', 'Fletching', 'Firemaking', 'Crafting',
      'Smithing', 'Herblore', 'Agility', 'Thieving', 'Slayer',
      'Farming', 'Runecrafting', 'Hunter', 'Construction'
    ];

    const skills = {};
    const bank = {};

    skillList.forEach(skill => {
      skills[skill] = { xp: 0, level: 1, tool: null };
    });

    const skillItems = {
      'Mining': 'Copper Ore',
      'Fishing': 'Shrimp',
      'Woodcutting': 'Logs'
    };

    const skillTools = {
      'Mining': [
        { name: 'Bronze Pickaxe', level: 1, xpBoost: 0 },
        { name: 'Iron Pickaxe', level: 10, xpBoost: 10 },
        { name: 'Steel Pickaxe', level: 20, xpBoost: 20 }
      ],
      'Fishing': [
        { name: 'Small Net', level: 1, xpBoost: 0 },
        { name: 'Fishing Rod', level: 15, xpBoost: 10 },
        { name: 'Fly Fishing Rod', level: 30, xpBoost: 20 }
      ],
      'Woodcutting': [
        { name: 'Bronze Axe', level: 1, xpBoost: 0 },
        { name: 'Iron Axe', level: 10, xpBoost: 10 },
        { name: 'Steel Axe', level: 20, xpBoost: 20 }
      ]
    };

    function renderSkills() {
      const div = document.getElementById('skills-display');
      div.innerHTML = skillList.map(skill => 
        `<strong>${skill}:</strong> ${skills[skill].level}`
      ).join(' | ');
    }

    function renderButtons() {
      const div = document.getElementById('buttons');
      div.innerHTML = skillList.map(skill =>
        `<button onclick="doSkill('${skill}')">${skill}</button>`
      ).join('');
    }

    function renderBank() {
      const div = document.getElementById('bank-display');
      let text = '<strong>Bank:</strong> ';
      if (Object.keys(bank).length === 0) {
        text += 'Empty';
      } else {
        text += Object.entries(bank).map(([item, qty]) => `${qty} ${item}`).join(' | ');
      }
      div.innerHTML = text;
    }

    function renderTools() {
      const div = document.getElementById('tools-display');
      let text = '<strong>Current Tools + Efficiency:</strong><br>';
      skillList.forEach(skill => {
        const tool = skills[skill].tool;
        if (skillTools[skill]) {
          if (tool) {
            text += `${skill}: ${tool.name} (Efficiency: +${tool.xpBoost} XP per action)<br>`;
          } else {
            text += `${skill}: No tool unlocked yet (Efficiency: +0 XP)<br>`;
          }
        } else {
          text += `${skill}: No tool required (Efficiency: +0 XP)<br>`;
        }
      });
      div.innerHTML = text;
    }

    function getBestTool(skillName, level) {
      if (!skillTools[skillName]) return null;
      let best = null;
      for (const tool of skillTools[skillName]) {
        if (level >= tool.level) best = tool;
      }
      return best;
    }

    function doSkill(skillName) {
      let baseXP = 50;
      const skill = skills[skillName];

      // Unlock best tool
      const tool = getBestTool(skillName, skill.level);
      skill.tool = tool;

      let bonusXP = tool ? tool.xpBoost : 0;
      let totalXP = baseXP + bonusXP;

      skill.xp += totalXP;
      const neededXP = skill.level * 100;
      if (skill.xp >= neededXP) {
        skill.xp -= neededXP;
        skill.level++;
        renderSkills();
        renderTools();
        addLog(`Congratulations, your ${skillName} level is now ${skill.level}!`, 'log-success');
      }
      addLog(`You gain ${totalXP} XP in ${skillName} ${tool ? `using ${tool.name}` : ''}.`, 'log-xp');

      if (skillItems[skillName]) {
        const item = skillItems[skillName];
        bank[item] = (bank[item] || 0) + 1;
        renderBank();
        addLog(`You gathered ${item}.`, 'log-success');
      }
    }

    // Init
    renderSkills();
    renderButtons();
    renderBank();
    renderTools();
  </script>
</body>
</html>