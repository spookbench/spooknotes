---
cssclass: cards
status: Done 
target: 
banner: "![[https://raw.githubusercontent.com/faroukx/obsidian-homepage/main/Farouk's%20Homepage%20-%20Shiba%20Uni/Obsidian/Attachements/faroukhomepage2.png]]"
banner_y: 0.34619
banner_x: 0.50867
banner_icon: 
banner_lock: true
---


```dataviewjs
let morning = [
    "Let's do great work today!",
    "It's a new day!"
];

let afternoon = [
    "Let's finish the day well!",
    "Let's keep up our focus!",
    "Need another cup of coffee? ☕"
];

let evening = [
    "Time to go home!",
    "Your family misses you!"
];

const currentHour = moment().format('HH'); 
let greeting; 

if (currentHour >= 17 || currentHour < 5) { 
    greeting = "🌙 Good Evening! "
        + evening[Math.floor(Math.random()*evening.length)];
} else if (currentHour >= 5 && currentHour < 12) { 
    greeting = "🌞 Good Morning! " +
        + morning[Math.floor(Math.random()*morning.length)];
} else { 
    greeting = "🌞 Good Afternoon! "
        + afternoon[Math.floor(Math.random()*afternoon.length)];
} 


dv.paragraph(greeting);

```

 

> [!cards|3]
>  **Self** 
> ![External Image|center|440](https://raw.githubusercontent.com/D3Ext/aesthetic-wallpapers/main/images/van.png)
>  **[[00. Obsidian\|Obsidian]] — #obsidian**  <br> **[[10. Personal\|Personal]] — #personal**   <br> **[[20. PKM Library\|PKM Library]] — #pkm**   <br>  **[[30. MOC List\|MOC List]] — #moc** 
>  
>  **Personal**
> ![External Image|center|440](https://raw.githubusercontent.com/D3Ext/aesthetic-wallpapers/main/images/pink-mecha.png)
>**[[40. Projects\|Workspace]] — #projects**  <br> **[[50. Journal\|Journal]] — #journal**  
>
>  **Others**
> ![External Image|center|440](https://raw.githubusercontent.com/D3Ext/aesthetic-wallpapers/main/images/wallhaven-28rjj6.png)
>**[[60. Life Areas\|Life Areas]] — #lifeareas**  <br> **[[70. Life Action\|Action Management]] — #action**  
>



>[!multi-column|right|2]
>
>> [!danger]+ Life Progress メメント・モリ
>> ![[Life Progress]]
>
>> [!important]+ Countdown カウントダウン
>> ![[Countdown]]

---