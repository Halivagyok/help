# Suli Code Help

## JAVA

### Conversion
```java
Integer.parseInt();
valtozo.toString();
```

### Class Structure
```java
private class Example {
    public String name;
    public int value;
    public boolean status;

    public Example(String row) {
        String[] s = row.split(";");
        this.name = s[0];
        this.value = Integer.parseInt(s[1]);
        this.status = Boolean.parseBoolean(s[2]);
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return String.format("%s (%s): %d g", name, name, value);
    }
}
```

### Read File into ArrayList
```java
private ArrayList<Example> list = new ArrayList<>();

public void read(String filename) {
    Scanner sc = null;
    try {
        sc = new Scanner(new File(filename), "utf-8");
        sc.nextLine(); // Skip header
        while(sc.hasNextLine()) {
            list.add(new Example(sc.nextLine()));
        }
    } catch (FileNotFoundException e) {
        throw new RuntimeException(e);
    } finally {
        if(sc != null) sc.close();
    }
}
```

### Write to File (PrintWriter)
```java
File f = new File("output.txt");
PrintWriter pw = null;
try {
    pw = new PrintWriter(f);
    for(int i = 0; i < list.size(); i++) {
       pw.println(list.get(i));
    }
} catch (FileNotFoundException e) {
    throw new RuntimeException(e);
} finally {
   if(pw != null) pw.close();
}
```

### System.out.printf Formatting
- `%d` : int
- `%s` : String
- `%b` : boolean
- `%f` : float (e.g., `%.2f` for 2 decimals)

```java
System.out.printf("%d", someInt);
```

### Random
```java
int rand = (int)(Math.random() * (max - min + 1)) + min;
int randIndex = (int)(Math.random() * list.size());
```

### Entryset (HashMap, TreeMap)
```java
for(var entry : map.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

## Grouping (TreeMap with containsKey)
```java
TreeMap<Integer, Integer> mL = new TreeMap<>();
for(Madarak m : madar){
    if (mL.containsKey(m.avgM)) {
        mL.put(m.avgM, mL.get(m.avgM) + 1);
    } else {
        mL.put(m.avgM, 1);
    }
}
```

### JavaFX - About Dialog
```java
@FXML 
public void showAbout() {
    Alert alert = new Alert(Alert.AlertType.INFORMATION);
    alert.setTitle("About");
    alert.setContentText("Project v1.0.0\n(C) 2026");
    alert.setHeaderText(null);

    // Title icon 
    ((Stage)alert.getDialogPane().getScene().getWindow()).getIcons().add(
        new Image(getClass().getResourceAsStream("icons/icon.png"))
    );

    // Graphic
    alert.setGraphic(new ImageView(new Image(getClass().getResourceAsStream("icons/icon.png"))));
    
    alert.showAndWait();
}
```

### JavaFX - Exit
```java
Platform.exit();
```

### JavaFX - FileChooser
```java
private FileChooser saveCh;
private FileChooser openCh;

public void initialize() {
    saveCh = new FileChooser();
    saveCh.setInitialDirectory(new File("./"));
    saveCh.setInitialFileName("save");
    saveCh.getExtensionFilters().add(new FileChooser.ExtensionFilter("txt files", "*.txt"));
    saveCh.setTitle("Save");

    openCh = new FileChooser();
    openCh.setInitialDirectory(new File("./"));
    openCh.getExtensionFilters().add(new FileChooser.ExtensionFilter("txt files", "*.txt"));
    openCh.setTitle("Open");
}
```

### JavaFX - Save Implementation
```java
public void save() {
    File f = saveCh.showSaveDialog(listContainer.getScene().getWindow());
    if(f == null) return;
    PrintWriter pw = null;
    try {
        pw = new PrintWriter(f);
        for (String row : listContainer.getItems()) pw.println(row);
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if (pw != null) pw.close();
    }
}
```

---

## BACKEND

### Setup
```bash
bun install express cors mysql2
```

### Imports
```javascript
import express from "express"
import cors from "cors"
import mysql from "mysql2/promise"
```

### Server Initialization
```javascript
const app = express()
app.use(express.json())
app.use(cors())

app.listen(8000, err => console.log(err ? err : "Server running on :8000"))
```

### Routes
```javascript
app.get("/", (req, res) => res.send("<h1>BANAN</h1>"))

// Request data
req.query   // /api/path?hossz=...
req.params  // /api/:id
req.body    // POST/PUT body
```

### MySQL Connection
```javascript
const con = await mysql.createConnection({
    host: "localhost",
    port: 3306,
    database: "db_name",
    user: "root",
    password: ""
})
```

### Queries
```javascript
try {
    let sql = "SELECT * FROM table WHERE name = ?"
    let [results] = await con.execute(sql, ["rick"])
} catch (err) {
    console.error(err);
}
```

### GET by ID
```javascript
app.get("/get/:id", (req, res) => {
    let { id } = req.params;

    if (id == null) {
        return res.status(400).send({error: "Missing parameter!"});
    }

    let index = data.findIndex(x => x.id == id);

    if (index == -1) {
        return res.status(404).send({error: "Not found!"});
    }

    res.status(200).send(data[index]);
})
```

### Search (LIKE)
```javascript
app.get("/api/search/:term", async (req, res) => {
    let { term } = req.params;
    try {
        const [results] = await con.query(
            'SELECT * FROM table WHERE column LIKE ?', 
            [`%${term}%`]
        );
        res.status(200).send(results);
    } catch(err) {
        res.status(500).send({error: "Database error", err});
    }
})
```

### POST (Insert)
```javascript
app.post("/add", async (req, res) => {
    const { a, b, c, d, id } = req.body;
    if (!a || !b || !c || !d || !id) {
        return res.status(400).send({ error: "Invalid parameters!" });
    }
    try {
        const [result] = await con.query(
            "INSERT INTO table (a, b, c, d, id) VALUES (?, ?, ?, ?, ?)", 
            [a, b, c, d, id]
        );
        res.status(201).send(result);
    } catch (error) {
        res.status(500).send({ error: error.message });
    }
});
```

### PUT (Update)
```javascript
app.put("/update/:id2", async (req, res) => {
    const { a, b, c, d, id1 } = req.body;
    const { id2 } = req.params;
    if (!a || !b || !c || !d || !id1 || !id2) {
        return res.status(400).send({ error: "Invalid parameters!" });
    }
    try {
        const [result] = await con.query(
            "UPDATE table SET a = ?, b = ?, c = ?, d = ?, id1 = ? WHERE id2 = ?", 
            [a, b, c, d, id1, id2]
        );
        if (result.affectedRows === 0) {
            return res.status(404).send({ error: "Not found!" });
        }
        res.status(200).send(result);
    } catch (error) {
        res.status(500).send({ error: error.message });
    }
});
```

### DELETE
```javascript
app.delete("/delete/:id", async (req, res) => {
    const { id } = req.params;
    if (!id) {
        return res.status(400).send({ error: "Invalid parameters!" });
    }
    try {
        const [result] = await con.query("DELETE FROM table WHERE id = ?", [id]);
        if (result.affectedRows === 0) {
            return res.status(404).send({ error: "Not found!" });
        }
        res.status(200).send(result);
    } catch (error) {
        res.status(500).send({ error: error.message });
    }
});
```

---

## FRONTEND

### Routing (React)
```jsx
<BrowserRouter>
    <Routes>
        <Route path="/" element={<Home/>} />
        <Route path="/path/:id1" element={<View1/>} />
        <Route path="/other/:id2" element={<View2/>} />
    </Routes>
</BrowserRouter>

// Programmatic navigation
const navigate = useNavigate();
navigate("/");
navigate("/path/" + value);
```

### Tailwind Setup (Vite)
```bash
bun i tailwindcss @tailwindcss/vite
```

**vite.config.js**:
```javascript
import tailwindcss from "@tailwindcss/vite"
// ... add to plugins: [tailwindcss()]
```

**index.css**:
```css
@import "tailwindcss";
```

### Data Fetching (State & Effect)
```jsx
import { useState, useEffect } from "react"

const [data, setData] = useState([])

useEffect(() => {
    const fetchData = async () => {
        const response = await fetch("http://localhost:8000/get");
        const json = await response.json();
        setData(json);
    }
    fetchData();
}, [])
```

### POST Fetch Example
```jsx
useEffect(() => {
    const sendData = async () => {
        const response = await fetch("http://localhost:8000/post", {
            method: "POST", // PUT, DELETE, PATCH
            headers: {"Content-Type": "application/json"},
            body: JSON.stringify({ /* data */ })
        });
        const json = await response.json();
    }
    sendData();
}, [])
```
