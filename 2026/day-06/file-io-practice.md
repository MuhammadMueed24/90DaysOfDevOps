# Day 06 – Linux Fundamentals: Read and Write Text Files

---

# 1. Create a File

### Command
```bash
touch notes.txt
echo "Line 1" > notes.txt
echo "Line 2" >> notes.txt
echo "Line 3" | tee -a notes.txt
```

# 2. Read the entire file

### Command
```bash
cat notes.txt
Line 1
Line 2
Line 3
```

# 3. Read First Lines of the File

### Command
```bash
head -n 2 notes.txt
Line 1
Line 2
```

# 4. Read Last Lines of the File

### Command
```bash
tail -n 2 notes.txt
Line 2
Line 3
```
