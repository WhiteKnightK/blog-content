---
title: "代码语法高亮示例"
date: 2024-05-04
author: "WhiteKnightK"
categories: ["Web Development", "Coding"]
tags: ["syntax", "markdown", "code"]
excerpt: "这篇文章展示了在博客中使用代码块和语法高亮的不同方式"
---

# 代码语法高亮示例

在技术博客中，清晰地展示代码是非常重要的。这篇文章将演示各种编程语言的代码高亮效果。

## JavaScript 代码示例

以下是一个简单的 JavaScript 函数示例：

```javascript
function calculateTotal(items) {
  return items
    .filter(item => item.price > 0)
    .map(item => item.price * item.quantity)
    .reduce((total, price) => total + price, 0);
}

// 使用箭头函数
const formatPrice = (price) => `$${price.toFixed(2)}`;

// 异步函数示例
async function fetchUserData(userId) {
  try {
    const response = await fetch(`https://api.example.com/users/${userId}`);
    const data = await response.json();
    return data;
  } catch (error) {
    console.error("获取用户数据失败:", error);
    return null;
  }
}
```

## TypeScript 代码示例

TypeScript 添加了类型注解：

```typescript
interface Product {
  id: string;
  name: string;
  price: number;
  quantity: number;
}

function calculateTotal(items: Product[]): number {
  return items
    .filter(item => item.price > 0)
    .map(item => item.price * item.quantity)
    .reduce((total, price) => total + price, 0);
}

// 泛型函数
function getFirstItem<T>(array: T[]): T | undefined {
  return array.length > 0 ? array[0] : undefined;
}
```

## Python 代码示例

下面是 Python 代码示例：

```python
def calculate_total(items):
    """
    计算所有商品的总价
    """
    return sum(item['price'] * item['quantity'] for item in items if item['price'] > 0)

# 类定义
class Product:
    def __init__(self, name, price, quantity=1):
        self.name = name
        self.price = price
        self.quantity = quantity
        
    def get_total(self):
        return self.price * self.quantity
    
    @property
    def is_available(self):
        return self.quantity > 0

# 异步函数
async def fetch_data(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.json()
```

## Go 代码示例

Go 语言的例子：

```go
package main

import (
    "fmt"
    "strings"
)

// 定义结构体
type Product struct {
    Name     string
    Price    float64
    Quantity int
}

// 方法
func (p Product) GetTotal() float64 {
    return p.Price * float64(p.Quantity)
}

func calculateTotal(products []Product) float64 {
    var total float64
    for _, product := range products {
        if product.Price > 0 {
            total += product.GetTotal()
        }
    }
    return total
}

func main() {
    products := []Product{
        {"笔记本电脑", 999.99, 1},
        {"鼠标", 25.50, 2},
        {"键盘", 45.99, 1},
    }
    
    fmt.Printf("总价: $%.2f\n", calculateTotal(products))
}
```

## Rust 代码示例

Rust 的例子：

```rust
#[derive(Debug)]
struct Product {
    name: String,
    price: f64,
    quantity: u32,
}

impl Product {
    fn new(name: &str, price: f64, quantity: u32) -> Self {
        Product {
            name: name.to_string(),
            price,
            quantity,
        }
    }
    
    fn get_total(&self) -> f64 {
        self.price * self.quantity as f64
    }
}

fn calculate_total(products: &[Product]) -> f64 {
    products
        .iter()
        .filter(|product| product.price > 0.0)
        .map(|product| product.get_total())
        .sum()
}

fn main() {
    let products = vec![
        Product::new("笔记本电脑", 999.99, 1),
        Product::new("鼠标", 25.50, 2),
        Product::new("键盘", 45.99, 1),
    ];
    
    println!("总价: ${:.2}", calculate_total(&products));
}
```

## HTML/CSS/JavaScript 组合示例

前端开发中常见的混合代码：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>产品列表</title>
    <style>
        .product-card {
            border: 1px solid #eaeaea;
            border-radius: 8px;
            padding: 16px;
            margin-bottom: 16px;
            transition: transform 0.3s ease;
        }
        
        .product-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
        }
        
        .product-title {
            color: #333;
            font-size: 18px;
            margin-bottom: 8px;
        }
        
        .product-price {
            color: #e44d26;
            font-weight: bold;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <div id="app">
        <h1>产品列表</h1>
        <div id="product-list"></div>
    </div>
    
    <script>
        const products = [
            { id: 1, name: "笔记本电脑", price: 999.99 },
            { id: 2, name: "鼠标", price: 25.50 },
            { id: 3, name: "键盘", price: 45.99 }
        ];
        
        function renderProducts() {
            const productList = document.getElementById('product-list');
            
            products.forEach(product => {
                const card = document.createElement('div');
                card.className = 'product-card';
                
                card.innerHTML = `
                    <h2 class="product-title">${product.name}</h2>
                    <p class="product-price">$${product.price.toFixed(2)}</p>
                    <button onclick="addToCart(${product.id})">添加到购物车</button>
                `;
                
                productList.appendChild(card);
            });
        }
        
        function addToCart(productId) {
            console.log(`添加商品 ${productId} 到购物车`);
            // 购物车逻辑...
        }
        
        document.addEventListener('DOMContentLoaded', renderProducts);
    </script>
</body>
</html>
```

## JSON 示例

数据结构示例：

```json
{
  "store": {
    "name": "我的在线商店",
    "products": [
      {
        "id": "p1",
        "name": "笔记本电脑",
        "price": 999.99,
        "specs": {
          "processor": "Intel i7",
          "memory": "16GB",
          "storage": "512GB SSD"
        },
        "inStock": true
      },
      {
        "id": "p2",
        "name": "鼠标",
        "price": 25.50,
        "specs": {
          "type": "Wireless",
          "dpi": 1600
        },
        "inStock": true
      },
      {
        "id": "p3",
        "name": "键盘",
        "price": 45.99,
        "specs": {
          "type": "Mechanical",
          "layout": "QWERTY"
        },
        "inStock": false
      }
    ],
    "location": {
      "address": "123 电子大道",
      "city": "科技城",
      "country": "中国"
    }
  }
}
```

## Bash 脚本示例

命令行操作示例：

```bash
#!/bin/bash

# 部署脚本示例
echo "开始部署应用..."

# 设置变量
APP_NAME="my-blog"
DEPLOY_DIR="/var/www/$APP_NAME"
GIT_REPO="https://github.com/username/$APP_NAME.git"

# 创建部署目录
if [ ! -d "$DEPLOY_DIR" ]; then
    echo "创建部署目录: $DEPLOY_DIR"
    mkdir -p "$DEPLOY_DIR"
fi

# 拉取最新代码
echo "拉取最新代码..."
cd "$DEPLOY_DIR"
git pull origin main || {
    echo "git pull 失败，尝试从头克隆..."
    rm -rf "$DEPLOY_DIR"/*
    git clone "$GIT_REPO" .
}

# 安装依赖
echo "安装依赖..."
npm install --production

# 构建应用
echo "构建应用..."
npm run build

# 重启服务
echo "重启服务..."
pm2 restart "$APP_NAME"

echo "部署完成!"
```

## SQL 示例

数据库查询示例：

```sql
-- 创建产品表
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    description TEXT,
    category_id INTEGER REFERENCES categories(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 插入示例数据
INSERT INTO products (name, price, description, category_id) 
VALUES 
    ('笔记本电脑', 999.99, '高性能笔记本电脑，适合办公和游戏', 1),
    ('鼠标', 25.50, '无线鼠标，1600DPI', 2),
    ('键盘', 45.99, '机械键盘，青轴', 2);

-- 查询示例
SELECT 
    p.id, 
    p.name, 
    p.price, 
    c.name AS category_name
FROM 
    products p
JOIN 
    categories c ON p.category_id = c.id
WHERE 
    p.price > 50
ORDER BY 
    p.price DESC;
```

## 行内代码示例

除了代码块外，还可以使用行内代码，例如当提到变量 `productId` 或函数 `calculateTotal()` 时，以及提到命令如 `npm install` 时。

## 使用代码注释

代码注释对于解释复杂逻辑非常重要：

```javascript
/**
 * 计算购物车中所有商品的总价
 * @param {Array} items - 购物车中的商品
 * @returns {number} - 总价
 */
function calculateTotal(items) {
  // 过滤掉价格不正确的商品
  const validItems = items.filter(item => item.price > 0);
  
  // 计算每个商品的总价
  const itemTotals = validItems.map(item => {
    // 应用折扣(如果有)
    const discount = item.discount || 0;
    const discountedPrice = item.price * (1 - discount);
    
    return discountedPrice * item.quantity;
  });
  
  // 将所有商品价格相加
  return itemTotals.reduce((total, price) => total + price, 0);
}
```

希望这个示例文件能够帮助您测试博客的代码块样式和语法高亮效果！ 