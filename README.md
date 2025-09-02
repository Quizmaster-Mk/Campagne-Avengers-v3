<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>Avengers Quest - Comics Edition</title>
<style>
  body { font-family: Arial, sans-serif; background-color: #111; color: #eee; padding: 20px; text-align: center; }
  .choice { margin: 10px 0; cursor: pointer; color: #0f0; }
  .choice:hover { text-decoration: underline; }
  #stats { font-weight: bold; margin-top: 10px; }
  img { max-width: 250px; display: block; margin: 10px auto; border: 2px solid #555; border-radius: 10px; background:#222; }
</style>
</head>
<body>

<h1>Avengers Quest - Comics Edition</h1>
<div id="scene">
    <p id="story"></p>
    <img id="heroImg" src="" alt="">
    <img id="enemyImg" src="" alt="">
    <div id="stats"></div>
    <div id="choices"></div>
</div>

<script>
let player = { hero: "", power: "", hp: 20, inventory: [] };
let enemies = {
    robot: { name: "Robot de Loki", hp: 10, maxHp: 10, img: "https://upload.wikimedia.org/wikipedia/commons/3/37/Robot_cartoon.svg" },
    ultron: { name: "Ultron", hp: 12, maxHp: 12, img: "https://upload.wikimedia.org/wikipedia/commons/6/6e/Ultron_marvel.png" },
    thanos: { name: "Thanos", hp: 20, maxHp: 20, img: "https://upload.wikimedia.org/wikipedia/commons/d/d3/Thanos_2014.png" }
};
let heroImages = {
    "Iron Man": "https://upload.wikimedia.org/wikipedia/commons/0/0e/Iron_Man_mark_III.png",
    "Thor": "https://upload.wikimedia.org/wikipedia/commons/0/06/Thor_marvel.png",
    "Black Widow": "https://upload.wikimedia.org/wikipedia/commons/5/5b/Black_Widow_marvel.png"
};

function randomDamage(base){ return Math.floor(base + Math.random()*3); }

const storyData = {
    start: {
        text: "Choisis ton Avenger :",
        choices: [
            { text: "Iron Man", next: "choosePower_IronMan" },
            { text: "Thor", next: "choosePower_Thor" },
            { text: "Black Widow", next: "choosePower_BlackWidow" }
        ]
    },
    choosePower_IronMan: {
        text: "Choisis ton pouvoir :",
        choices: [
            { text: "Répulseurs", next: "mission1", setHero:"Iron Man", setPower:"Répulseurs" },
            { text: "Vol Supersonique", next: "mission1", setHero:"Iron Man", setPower:"Vol Supersonique" }
        ]
    },
    choosePower_Thor: {
        text: "Choisis ton pouvoir :",
        choices: [
            { text: "Mjolnir (Foudre)", next: "mission1", setHero:"Thor", setPower:"Foudre" },
            { text: "Contrôle météo", next: "mission1", setHero:"Thor", setPower:"Météo" }
        ]
    },
    choosePower_BlackWidow: {
        text: "Choisis ton pouvoir :",
        choices: [
            { text: "Arts martiaux", next: "mission1", setHero:"Black Widow", setPower:"Arts martiaux" },
            { text: "Infiltration", next: "mission1", setHero:"Black Widow", setPower:"Infiltration" }
        ]
    },
    mission1: {
        text: "Mission 1: Loki attaque la ville ! Choisis un gadget :",
        choices: [
            { text: "Bouclier énergétique", next: "combatRobot", addItem:"Bouclier" },
            { text: "Grenade EMP", next: "combatRobot", addItem:"Grenade" }
        ]
    },
    combatRobot: {
        text: "",
        choices: [],
        resolve: function(){
            const enemy = enemies.robot;
            const dmgToEnemy = randomDamage((player.hero==="Thor" && player.power==="Foudre")?10:5) + (player.inventory.includes("Grenade")?2:0);
            const dmgToPlayer = randomDamage(3) - (player.inventory.includes("Bouclier")?1:0);

            enemy.hp -= dmgToEnemy;
            player.hp -= dmgToPlayer;

            let storyText = `⚔️ Tu infliges ${dmgToEnemy} dégâts au ${enemy.name}. Il riposte et tu perds ${dmgToPlayer} HP.`;
            storyText += `\n${enemy.name} HP: ${Math.max(enemy.hp,0)} / ${enemy.maxHp}, Ton HP: ${player.hp}`;

            document.getElementById("story").textContent = storyText;
            document.getElementById("heroImg").src = heroImages[player.hero];
            document.getElementById("enemyImg").src = enemy.img;
            document.getElementById("stats").textContent = "Inventaire: "+player.inventory.join(", ");

            if(player.hp<=0){ 
                document.getElementById("choices").innerHTML="";
                document.getElementById("story").textContent+="\n☠️ Tu es vaincu... FIN."; 
            }
            else if(enemy.hp<=0){ 
                document.getElementById("choices").innerHTML="";
                document.getElementById("story").textContent+="\n💥 "+enemy.name+" est détruit ! Mission 1 terminée."; 
                setTimeout(()=>updateStory(storyData.mission2),3000); 
            }
            else{ setTimeout(()=>updateStory(storyData.combatRobot),3000); }
        }
    },
    mission2: {
        text: "Mission 2: Ultron menace le pont ! Choisis ton approche :",
        choices:[
            { text:"Attaque frontale", next:"combatUltron" },
            { text:"Protéger civils avec gadget", next:"strategieUltron" }
        ]
    },
    combatUltron: {
        text:"",
        choices:[],
        resolve:function(){
            const enemy=enemies.ultron;
            const dmgToEnemy=randomDamage((player.hero==="Iron Man" && player.power==="Vol Supersonique")?8:5)+(player.inventory.includes("Grenade")?2:0);
            const dmgToPlayer=randomDamage(4)-(player.inventory.includes("Bouclier")?1:0);

            enemy.hp-=dmgToEnemy;
            player.hp-=dmgToPlayer;

            let storyText=`⚔️ Tu infliges ${dmgToEnemy} dégâts à ${enemy.name}. Il riposte et tu perds ${dmgToPlayer} HP.`;
            storyText+=`\n${enemy.name} HP: ${Math.max(enemy.hp,0)} / ${enemy.maxHp}, Ton HP: ${player.hp}`;

            document.getElementById("story").textContent=storyText;
            document.getElementById("heroImg").src=heroImages[player.hero];
            document.getElementById("enemyImg").src=enemy.img;
            document.getElementById("stats").textContent="Inventaire: "+player.inventory.join(", ");

            if(player.hp<=0){ 
                document.getElementById("choices").innerHTML=""; 
                document.getElementById("story").textContent+="\n☠️ Tu es vaincu... FIN."; 
            }
            else if(enemy.hp<=0){ 
                document.getElementById("choices").innerHTML=""; 
                document.getElementById("story").textContent+="\n💥 "+enemy.name+" est vaincu ! Mission 2 terminée."; 
                setTimeout(()=>updateStory(storyData.mission3),3000); 
            }
            else{ setTimeout(()=>updateStory(storyData.combatUltron),3000); }
        }
    },
    strategieUltron: {
        text:"",
        choices:[],
        resolve:function(){
            player.hp-=2;
            document.getElementById("story").textContent=`⚠️ Tu protèges les civils mais subis 2 dégâts. HP restante: ${player.hp}`;
            document.getElementById("stats").textContent="Inventaire: "+player.inventory.join(", ");
            if(player.hp<=0){ 
                document.getElementById("choices").innerHTML=""; 
                document.getElementById("story").textContent+="\n☠️ Tu es vaincu... FIN."; 
            }
            else{ setTimeout(()=>updateStory(storyData.mission3),3000); }
        }
    },
    mission3:{
        text:"Mission 3: Thanos avec le Gant de l'Infini ! Choisis ton approche :",
        choices:[
            { text:"Combattre Thanos directement", next:"combatThanos" },
            { text:"Allier forces avec allié", next:"alliance" }
        ]
    },
    combatThanos:{
        text:"",
        choices:[],
        resolve:function(){
            const enemy=enemies.thanos;
            const dmgToEnemy=randomDamage((player.hero==="Thor" && player.power==="Foudre")?10:5)+(player.inventory.includes("Bouclier")?2:0);
            const dmgToPlayer=randomDamage(5);

            enemy.hp-=dmgToEnemy;
            player.hp-=dmgToPlayer;

            let storyText=`⚔️ Tu infliges ${dmgToEnemy} dégâts à ${enemy.name}. Il riposte et tu perds ${dmgToPlayer} HP.`;
            storyText+=`\n${enemy.name} HP: ${Math.max(enemy.hp,0)} / ${enemy.maxHp}, Ton HP: ${player.hp}`;

            document.getElementById("story").textContent=storyText;
            document.getElementById("heroImg").src=heroImages[player.hero];
            document.getElementById("enemyImg").src=enemy.img;
            document.getElementById("stats").textContent="Inventaire: "+player.inventory.join(", ");

            if(player.hp<=0){ 
                document.getElementById("choices").innerHTML=""; 
                document.getElementById("story").textContent+="\n☠️ Tu es vaincu... FIN."; 
            }
            else if(enemy.hp<=0){ 
                document.getElementById("choices").innerHTML=""; 
                document.getElementById("story").textContent+="\n💥 "+enemy.name+" est vaincu ! Tu sauves la planète. FIN."; 
            }
            else{ setTimeout(()=>updateStory(storyData.combatThanos),3000); }
        }
    },
    alliance:{
        text:"",
        choices:[],
        resolve:function(){
            document.getElementById("story").textContent="🤝 Avec l'aide de tes alliés, vous vainquez Thanos ensemble ! La planète est sauvée. FIN.";
            document.getElementById("choices").innerHTML="";
        }
    }
};

function updateStory(node){
    if(node.setHero) player.hero=node.setHero;
    if(node.setPower) player.power=node.setPower;
    if(node.addItem) player.inventory.push(node.addItem);

    document.getElementById("stats").textContent = "HP: "+player.hp+" | Inventaire: "+player.inventory.join(", ");

    if(node.resolve){ node.resolve(); return; }

    document.getElementById("story").textContent = node.text;
    document.getElementById("heroImg").src = player.hero?heroImages[player.hero]:"";
    document.getElementById("enemyImg").src = "";

    const choicesDiv = document.getElementById("choices");
    choicesDiv.innerHTML = "";
    if(node.choices){
        node.choices.forEach(choice=>{
            const btn=document.createElement("div");
            btn.textContent=choice.text;
            btn.className="choice";
            btn.onclick=()=>updateStory(storyData[choice.next]);
            choicesDiv.appendChild(btn);
        });
    }
}

updateStory(storyData.start);
</script>

</body>
</html>
