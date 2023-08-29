
Jag programmerar mycket C för inbyggda system av olika slag. På ett sätt är det ju ett enklare språk än t.ex. C++ eftersom det är betydligt mindre. Men precis som du antyder är det också mycket lätt att råka göra fel. 
- Se till att slå på alla varningar du kan få från kompilatorn.
- Använd en så modern kompilator som möjligt och C99 (eller GNU99). 
- Undvik casts närhelst det är möjligt. Kod som innehåller casts hit och dit är ett tydligt tecken på att man gjort tokigt och ett ypperligt sätt att skjuta sig i foten. 
- Är det ett inbyggt system du bygger för bör du undvika dynamisk minnes allokering. 
- Använd standardfunktioner när det är möjligt, t.ex. för strängoperationer och annat. 
- Lär dig vad volatile, static är till för, använd const så ofta du kan. 
- Skriv tydlig och läsbar kod. Kompilatorn sköter optimeringen åt dig. 
- Finns en hel del lurigheter, t.ex. integer promotion och vad som händer då man subtraherar 2 undigned typer. 
- Använd typerna från stdint.h t.ex. uint32_t när du vill ha koll på typen. En int och en long är olika stor på olika plattformar. 
- Använd assert (eller någon liknande variant) samt static_assert för att säkerställa att dina antaganden stämmer nu och i framtiden. 

Annat:

Referens: 