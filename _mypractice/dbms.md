```cpp
db.Orders.find({ city: { $exists: true, $eq: "Pune" } }).count(); // count of orders in pune

db.Orders.find(
	{ "products.name": {$all: ["Shoes", "Cloth"] } },
	{ _id: 0, customer_name: 1 }
);

db.Orders.find({}, { _id: 0, customer_name: 1, totalAmount: 1 })
	.sort({ totalAmount: -1 })
	.limit(3);

var mapFunc = function() { emit(this.order_id, this.totalAmount); };
var reduceFunc = function(key, values) { return Array.sum(values); };
db.Orders.mapReduce(mapFunc, reduceFunc, { out: "total_price_per_order" });
db.total_price_per_order.find();

db.Employee.find(
	{ $expr: { $eq: [{ $dayOfMonth: "$join_date" }, 20] } },
	{ _id: 0, empid: 1, ename: 1, dept_no: 1, skills: 1 }
);

db.Employee.aggregate([
{
	$addFields: {
	experienceYears: {
	$dateDiff: { startDate: "$join_date", endDate: "$$NOW", unit: "year" }
		}
	}
},
	{ $project: { _id: 0, empid: 1, ename: 1, join_date: 1, experienceYears: 1 } }
]);

db.Employee.find(
{
	department: "FE",
	ename: { $regex: /^[sm]/i },
	skills: "Programming"
	},
	{ _id: 0, empid: 1, ename: 1, department: 1, skills: 1 }
);

db.Orders.find({ order_date: { $lt: new Date("2022-04-01") } });

db.Orders.updateMany(
	{ customer_name: "ABC" },
	{ $set: { order_date: new Date() } }
);

db.Student.find(
  { $expr: { $eq: [{ $dayOfMonth: "$birthdate" }, 20] } },
  { _id: 0, student_id: 1, student_name: 1, dept_no: 1, skills: 1 }
);

db.Student.aggregate([
  {
    $addFields: {
      age: {
        $dateDiff: { startDate: "$birthdate", endDate: "$$NOW", unit: "year" }
      }
    }
  },
  { $match: { age: { $gte: 18, $lte: 20 }, department: "ETC" } },
  { $sort: { student_name: 1 } },
  { $project: { _id: 0, student_name: 1, age: 1, department: 1 } }
]);

db.Orders.updateOne(
  { order_id: 3, customer_name: "ABC" },
  { 
    $push: { 
      products: { name: "Keyboard", quantity: 2 } 
    } 
  }
);
```

# ✅ **1. BASIC FIND QUERIES**

```
db.Collection.find();
db.Collection.find({ field: value });
db.Collection.find({ field: { $gt: 10 } });
db.Collection.find({ field: { $lt: 20 } });
db.Collection.find({ field: { $gte: 10, $lte: 50 } });
db.Collection.find({ field: { $in: ["A", "B"] } });
db.Collection.find({ field: { $regex: /^s/i } });   // starts with S

```

---

# ✅ **2. PROJECTIONS**

```
db.Collection.find(
  { condition },
  { _id: 0, field1: 1, field2: 1 }
);

```

---

# ✅ **3. SORTING**

```
db.Collection.find().sort({ field: 1 });   // ascending
db.Collection.find().sort({ field: -1 });  // descending

```

---

# ✅ **4. COUNTING**

```
db.Collection.countDocuments();
db.Collection.countDocuments({ condition });

```

---

# ✅ **5. ARRAY QUERIES**

```
db.Orders.find({ "products.name": "Shoes" });

db.Orders.updateOne(
  { order_id: 3 },
  { $push: { products: { name: "Keyboard", quantity: 2 } } }
);

db.Orders.aggregate([
  { $match: { order_id: 2 } },
  { $project: { totalProducts: { $size: "$products" } } }
]);

```

---

# ✅ **6. UPDATE OPERATIONS**

### **Change fields**

```
db.Collection.updateOne(
  { condition },
  { $set: { field1: "new value", field2: 123 } }
);

```

### **Update array item quantity**

```
db.Orders.updateOne(
  { "products.name": "Laptop" },
  { $set: { "products.$.quantity": 3 } }
);

```

---

# ✅ **7. DELETE DOCUMENT**

```
db.Collection.deleteOne({ Pincode: 576201 });

```

---

# ✅ **8. DATE OPERATIONS**

### **Date comparison**

```
db.Orders.find({ order_date: { $lt: new Date("2022-04-01") } });

```

### **Birth date / Join date = 20th**

```
db.Student.find(
  { $expr: { $eq: [{ $dayOfMonth: "$birthdate" }, 20] } }
);

```

### **Calculate age / experience**

```
db.Collection.aggregate([
  {
    $addFields: {
      age: {
        $dateDiff: { startDate: "$birthdate", endDate: "$$NOW", unit: "year" }
      }
    }
  }
]);

```

---

# ✅ **9. AGGREGATION FRAMEWORK**

### **Group by & count**

```
db.Collection.aggregate([
  { $group: { _id: "$department", total: { $sum: 1 } } }
]);

```

### **Department with maximum employees/salary**

```
db.Collection.aggregate([
  { $group: { _id: "$department", total: { $sum: "$salary" } } },
  { $sort: { total: -1 } },
  { $limit: 1 }
]);

```

### **Top 3 buyers**

```
db.Orders.find().sort({ totalAmount: -1 }).limit(3);

```

---

# ✅ **10. MAPREDUCE**

```
var mapFunc = function() { emit(this.department, 1); };
var reduceFunc = function(key, values) { return Array.sum(values); };

db.Collection.mapReduce(mapFunc, reduceFunc, { out: "result" });

db.result.find();

```

---

# ✅ **11. INDEX OPERATIONS**

### **Create Index**

```
db.Collection.createIndex({ field: 1 });

```

### **View Index**

```
db.Collection.getIndexes();

```

### **Drop Index**

```
db.Collection.dropIndex({ field: 1 });
// OR
db.Collection.dropIndex("field_1");

```

---

# ✅ **12. MOST COMMON PATTERNS USED IN EXAM**

### **Find orders with amount > 20000**

```
db.Orders.find({ totalAmount: { $gt: 20000 } });

```

### **Find students from Baroda/Ahmedabad & CSE**

```
db.Student.find({
  address: { $in: ["Baroda", "Ahmedabad"] },
  department: "CSE"
});

```

### **Find employees with salary between 30000–45000**

```
db.Employee.find({ salary: { $gte: 30000, $lte: 45000 } });

```