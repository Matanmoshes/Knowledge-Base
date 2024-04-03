
  

כל מערכת הפעלה מובססת unix או linux הכל קובץ או פרוסס.

  

מנהל החבילות (Package Manager)

מנהל חבילות בלינוקס הוא כלי שמסייע בניהול חבילות תוכנה - התקנה, עדכון, והסרה - באופן אוטומטי ומסודר. כל חבילה כוללת מידע על תוכנה כלשהי ואילו תלויות (dependencies) נדרשות להתקנה והפעלה שלה. מנהל החבילות דואג לפתרון התלויות האלו, מאפשר עדכונים קלים ומספק דרך נוחה לשמירה על מערכת ההפעלה מעודכנת ובטוחה.

  

דוגמאות למנהלי חבילות בלינוקס:

  

apt (Advanced Package Tool): משמש בדרך כלל במערכות הפעלה כמו Debian ו-Ubuntu.

  

לדוגמה, התקנת חבילה:

sudo apt-get install package-name

כאן, package-name הוא שם החבילה שברצונך להתקין.

  

yum (Yellowdog Updater, Modified): משמש במערכות הפעלה כמו CentOS ו-RHEL (Red Hat Enterprise Linux).

  

לדוגמה, התקנת חבילה:

sudo yum install package-name

dnf (Dandified Yum): גרסה מודרנית יותר של yum, משמשת ב-Fedora.

  

לדוגמה, התקנת חבילה:

sudo dnf install package-name

pacman: משמש ב-Arch Linux.

  

לדוגמה, התקנת חבילה:

sudo pacman -S package-name

מנהל החבילות מקל על המשתמשים ועל מנהלי המערכת לנהל את התוכנה שלהם בצורה קלה ויעילה, מבלי להצטרך להוריד ולהתקין ידנית את כל חבילה ואת תלויותיה.

  
  

מערכת קבצים (File System):  
  

מערכת קבצים היא שיטה שבה מערכות הפעלה מארגנת ומנהלת נתונים במכשירי אחסון, כמו דיסקים קשיחים, ציינו גם שבמחשב אחד יכולות להיות מספר מערכות קבצים שונות, כאשר כל אחת מותקנת במחיצה נפרדת או משמשת למערכת הפעלה שונה. זה מאפשר גמישות ותאימות עם תוכנות ומערכות שונות.

  

VFS (Virtual File System):  
שכבה אבסטרקטית במערכת הפעלה שמאפשרת לה לתמוך במגוון רחב של מערכות קבצים באופן שקוף.

  

Journaling:  
טכניקה במערכת קבצים שמסייעת למנוע אובדן או פגיעה בנתונים במקרה של כשל במערכת, על ידי תיעוד שינויים לפני שהם מתבצעים בפועל.

  

Common File System:

ext4: זו הגרסה הרביעית והחדישה ביותר של המערכת קבצים המורחבת (Extended File System) שנמצאת בשימוש נרחב במערכות הפעלה מבוססות לינוקס.

  

NTFS (New Technology File System):  
מערכת קבצים המתוחזקת על ידי מיקרוסופט, נפוצה בעיקר windows.

  

HFS (Hierarchical File System):  
מערכת קבצים שנוצרה על ידי אפל לשימוש במערכות הפעלה של מקינטוש.

  

פקודת df:  
פקודה במערכת הפעלה שמשמשת להצגת המקום הפנוי והנמצא בשימוש במערכות הקבצים המחוברות למערכת.

![](https://lh7-us.googleusercontent.com/NZYZcf0PQ_5991hp9jKegZnRoF1nIPVEYy1T5j9ZidrIQB9KWXMrt1tgsv7SlH-YhNn0wCLGAdxDbsiPmQB8O6pq4rCsA2E9hkzuccCwpKWDygWqDeW2mH7CU9TX6-_bVFgETNkCvA7NcTpAUjO8aMM)

  
  
  

בכדי להבין ולעבוד עם מערכת הפעלה לינוקס, יש כמה תיקיות חשובות שכדאי לזכור:

  

/bin: מכילה פקודות בסיסיות וחיוניות לשימוש המשתמש והמערכת.

/etc: מכילה קבצי תצורה לשירותים ותוכניות במערכת.

/home: מכילה את תיקיות המשתמשים האישיות.

/usr: מרכזת רוב היישומים והקבצים לשימוש כללי.

/var: מכילה קבצים שתוכנם משתנה, כמו לוגים וקבצי דואר.

/root: התיקייה האישית של משתמש ה-root, מנהל המערכת.

  

Block storage:  
חולק נתונים לבלוקים בעלי גודל קבוע עם כתובות ייחודיות, מה שמאפשר גישה מהירה ואקראית לכל בלוק. זה נפוץ במערכות גדולות כמו מחשוב ענן ומסדי נתונים, שם יעילות ומהירות גישה הם קריטיים. Block storage מתאים במיוחד ליישומים שדורשים טיפול עצמאי בכל חתיכת נתונים.

  

Inode:

זה מבנה נתונים בfile system מאחסן meta data על קבצים כמו הרשאות לקבצים וגודל הקובץ.  
כל תקיה או קובץ מיוצג על ידי inode  
Ls -i  
  

Partitioning  
Partitioning היא התהליך של חלוקת דיסק קשיח למספר מחיצות (partitions) נפרדות, שכל אחת מתפקדת כמו דיסק נפרד. כל מחיצה יכולה להכיל מערכת קבצים שונה ומשמשת למטרות שונות כמו אחסון נתונים, קבצי מערכת, או גיבויים.

Df

  

DevEx (Developer Experience)  
בפיתוח תוכנה מתייחס לכלל החוויות, הכלים והתהליכים שמופעלים כדי להקל ולשפר את עבודת מפתחי התוכנה. המטרה היא להגביר את היעילות, להפחית את מכשולי הפיתוח, ולייצר סביבת עבודה נעימה ותומכת שמעודדת יצירתיות וחדשנות.

  

**מדידית devex:**

1. Response time
    
2. Error rate
    
3. Adoption rate 
    
4. Developer satisfaction
    
5. Time to market 

  

Bash Script

[https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)

  
  

VIM editor

[https://phoenixnap.com/kb/wp-content/uploads/2021/11/vim-commands-cheat-sheet-by-pnap.pdf](https://phoenixnap.com/kb/wp-content/uploads/2021/11/vim-commands-cheat-sheet-by-pnap.pdf)

  
  
  

**User Management Commands**

Create a New User

sudo useradd johndoe

  

Modify a User's Account (e.g., Add to Group)

sudo usermod -aG sudo johndoe

  

Delete a User Account

sudo userdel johndoe

  

Change a User's Password

sudo passwd johndoe

  

Display User and Group Information

id johndoe

  

Display the Current User

whoami

  

Switch User

su - johndoe

  

Change File Ownership

sudo chown johndoe:users example.txt

  

Group Management Commands

Create a New Group

sudo groupadd editors

  

Modify a Group (Change Group Name)

sudo groupmod -n neweditors editors

  

Delete a Group

sudo groupdel editors

  

List Groups for a User

groups johndoe

  

Change Group Ownership of a File

sudo chgrp editors example.txt

  

Miscellaneous Commands

Show Last System Logins

last

  
  
  

Get User Information (if finger is installed)

finger johndoe

  

Get User Entry from /etc/passwd

getent passwd johndo

Users

  
  

קובץ /etc/passwd במערכת לינוקס מכיל את פרטי המשתמשים במערכת. כל שורה בקובץ מייצגת משתמש אחד ומכילה מידע מפורט עליו. הנה דוגמה לשורה אופיינית בקובץ /etc/passwd:

  

dan: x:1001:1001:Dan Smith,,,:/home/dan:/bin/bash

נפרט כל חלק בשורה:

  

1. שם המשתמש (dan): הזיהוי של המשתמש במערכת.
    

  

2. סיסמה (x): במערכות מודרניות, הסיסמאות מאוחסנות בקובץ נפרד (/etc/shadow) מסיבות ביטחון, ולכן כאן מופיע רק סימן x.
    

  

3. מספר המשתמש (UID - User ID, 1001): מספר ייחודי המזהה את המשתמש במערכת.
    

  

4. מספר קבוצת המשתמש (GID - Group ID, 1001): מספר ייחודי המזהה את קבוצת המשתמש העיקרית.
    

  

5. תיאור ופרטים נוספים (Dan Smith,,,): שדה לתיאור נוסף על המשתמש, לעיתים כולל שם מלא, פרטי קשר ועוד.
    

  

6. בית המשתמש (/home/dan): הנתיב לתיקיית הבית של המשתמש.
    

  

7. השל (Shell, /bin/bash): התוכנה שתרוץ כאשר המשתמש ייכנס למערכת, בדרך כלל מעטפת פקודה (shell).
    

  

8. קובץ /etc/passwd מאפשר למנהלי מערכת לראות בקלות את כל המשתמשים הרשומים במערכת ופרטים רלוונטיים אודותם.
    
  
  
  

**Groups**

עקרונות בסיסיים:

1. מזהה קבוצה (GID - Group ID): לכל קבוצה יש מזהה ייחודי, שנקרא GID, המשמש לזיהוי הקבוצה במערכת.
    

  

2. חברי הקבוצה: יוזרים ששייכים לקבוצה יכולים לרשת את ההרשאות המוגדרות לקבוצה זו.
    

  

3. קבוצת היוזרים הראשית (Primary Group): לכל יוזר יש קבוצה ראשית, שמשתמשת לקביעת הרשאות ברירת המחדל לקבצים חדשים שהוא יוצר.
    

  

דוגמה ליצירת קבוצה:

1. יצירת קבוצה חדשה: ניתן ליצור קבוצה חדשה באמצעות הפקודה groupadd. לדוגמה, כדי ליצור קבוצה בשם "developers":

  

sudo groupadd developers

  

2. הוספת יוזר לקבוצה: כדי להוסיף יוזר קיים לקבוצה החדשה, ניתן להשתמש בפקודה usermod. לדוגמה, כדי להוסיף את המשתמש "dan" לקבוצה "developers":

  

sudo usermod -aG developers dan

האופציה -aG מבטיחה שהיוזר יתווסף לקבוצה ללא הסרתו מקבוצות אחרות.

  

קבוצות בלינוקס מאפשרות ניהול יעיל של הרשאות עבור משתמשים רבים, ומשפרות את האבטחה ואת יכולת הניהול של מערכות מורכבות.

  

קובץ /etc/group בלינוקס מכיל את פרטי הקבוצות במערכת.  
 

developers:x:1002:alice,bob,charlie

נפרט כל חלק בשורה:

  

1. שם הקבוצה (developers): השם של הקבוצה.
    

  

2. סיסמה (x): ברוב המקרים, סיסמאות קבוצה לא נמצאות בשימוש ומוחלפות בתו 'x'. פרטים על סיסמאות קבוצה נמצאים בקובץ /etc/gshadow.
    

  

3. מספר הקבוצה (GID - Group ID, 1002): מספר ייחודי המזהה את הקבוצה במערכת.
    

  

4. חברי הקבוצה (alice,bob,charlie): רשימת שמות המשתמשים השייכים לקבוצה זו. בדוגמה זו, היוזרים 'alice', 'bob', ו-'charlie' הם חלק מהקבוצה 'developers'.
    

  
  
  

Files Permittions

![](https://lh7-us.googleusercontent.com/Il_4fwe40Pi0XDt_7PzbovKi0fMzuJVoHksUK0XlijuJ-GQjUT_tk7cFYaWHQSEcpXjRJW10tiB8o_j1ZyLWMDc8VpJ_tes2E2jfTfp96nNWvw3rtciEjWEEyvIMIffvgnPptKqUIhAETo4R7Iac7kc)

  

הרשאות קבצים בלינוקס מגדירות מי יכול לקרוא, לכתוב או להריץ קובץ או תיקייה. הרשאות אלה מחולקות לשלוש קבוצות: משתמש (user), קבוצה (group), ואחרים (others).

  

דוגמה להרשאות קובץ:

  

-rwxr--r--

והנה הפירוט:

  

סוג הקובץ: התו הראשון מציין את סוג הקובץ. - מציין קובץ רגיל. תווים אחרים יכולים לציין תיקייה (d), קישור סמלי (l), ועוד.

  

הרשאות המשתמש: שלושת התווים הבאים (rwx) מציינים את ההרשאות של בעל הקובץ: קריאה (r), כתיבה (w), והרצה (x). במקרה זה, בעל הקובץ יכול לעשות הכל.

  

הרשאות הקבוצה: שלושת התווים הבאים (r--) מציינים את ההרשאות לקבוצה: רק קריאה.

  

הרשאות לאחרים: שלושת התווים האחרונים (r--) זהים לקבוצה: רק קריאה.

  
  
  
  

**chown**

  

הפקודה chown בלינוקס משמשת לשינוי הבעלות על קבצים ותיקיות. היא מאפשרת לשנות את המשתמש ו/או את הקבוצה הבעלים של קובץ או תיקייה.

  

סינטקסיס בסיסי:

  

sudo chown [משתמש]:[קבוצה] [שם הקובץ או התיקייה]

  

[משתמש]: שם המשתמש החדש שתהיה לו הבעלות על הקובץ או התיקייה.

[קבוצה]: שם הקבוצה החדשה שתהיה לה הבעלות. אם לא צוין שם קבוצה, הבעלות של הקבוצה לא תשתנה.

[שם הקובץ או התיקייה]: הנתיב לקובץ או לתיקייה שעליהם יש לשנות את הבעלות.

דוגמה:

  

sudo chown alice:developers file.txt

  

פקודה זו משנה את הבעלות על הקובץ file.txt כך שהמשתמש alice יהיה הבעלים והקבוצה developers תהיה הקבוצה הבעלת.

  

שימושים נפוצים:

שינוי בעלות קובץ או תיקייה למשתמש אחר.

שינוי קבוצת בעלות קובץ או תיקייה.

הערות:

פקודת chown דורשת הרשאות מנהל (root), לכן בדרך כלל משתמשים בה עם sudo.

שינוי בעלות יכול להשפיע על גישה והרשאות, ולכן יש להשתמש בו בזהירות.

אפשרות לשינוי בעלות רקורסיבי (לתיקיות ולכל תכני התיקיות) ניתנת על ידי הוספת הדגל -R.

  
  
  
  
  
  
  
  
**