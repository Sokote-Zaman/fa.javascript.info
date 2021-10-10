پاسخ: `1`.

```js run
let i = 3;

while (i) {
  alert( i-- );
}
```

در هر بار تکرار حلقه متغیر `i` به مقدار `1` عدد کم می شود. عبارت `while(i)` وقتی به `i = 0` برسد متوقف می شود.

حالا گام های حلقه به این صورت است ("تشریح حلقه"):

```js
let i = 3;

alert(i--); // عدد 3 را نشان میدهد و متغیر را به 2 کاهش میدهد

alert(i--); // عدد 2 را نشان میدهد و متغیر را به 1 کاهش میدهد 

alert(i--); // عدد 1 را نشان میدهد و متغیر را به 0 کاهش میدهد

// حلقه را خاتمه میدهد while(i) تمام. عبارت
```