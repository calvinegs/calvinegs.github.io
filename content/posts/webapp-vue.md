---
title: "使用 Vue 來建立網路應用程式"
date: 2022-08-26
description: "這是三篇紀錄使用不同的三個 framework(Angular、React、Vue)來建立功能一模一樣的網路應用程式的筆記中的第三篇， 本篇中是使用 Vue 來建立 Web APP"
tags: ["react", "bootstrap", "vuex", "vue router", "npm-run-all", "axios", "Node.js", "express", "Json Server"]
categories: ["react"]
---
*[github Source code](https://github.com/calvinegs/vueapp.git)*


Angular、React、Vue 是三個最受歡迎的前端框架，這是三篇筆記分別使用這三個不同的框架來建立功能一模一樣的網路應用程式中的第三篇 - 使用 Vue 來建立網路應用程式。

這個網路應用程式的需求
- 産品訂購功能
    - 表頭：訂購商品總數、總額及送出訂單功能
    - 商品過濾功能：依産品類別來顯示商品
    - 商品清單：顯示商品明細、商品售價、訂購數量下拉選單及加入購物車等功能
- 訂單確認功能：顯示訂購商品明細及總金額
- 訂購成功功能：顯示訂購完成資訊

![image](https://user-images.githubusercontent.com/21993717/186826007-620a3a0d-5620-4540-a968-8fd597dbbd52.png)

![image](https://user-images.githubusercontent.com/21993717/186826123-34467e2b-a6e9-48aa-9e61-ff493bedb31e.png)

![image](https://user-images.githubusercontent.com/21993717/186826198-665ef3c3-c227-4a57-a04c-c968cd78ef0d.png)

![2022-08-26 13-01-06 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/186826234-5ecfcec7-a171-41d4-bf94-301aef0b1da9.png)



使用技術:

- Vue 3
- Bootstrap 5 (UI Framework)
- Vue Router 4
- Vuex 4
- axios 0.27
- NodeJS 18.5 & Express 4.18
- json-server 0.17.0
- npm-run-all 4.1.5 (npm 套件)
- @vue/cli

## 建立新專案

使用下列的 npx 語法即可建立最新版 Vue 預設的應用程式結構

```bash
$ npx @vue/cli create vueapp

Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to 
toggle all, <i> to invert selection, and <enter> to proceed)
 ◉ Babel
 ◉ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◉ Router
❯◉ Vuex
 ◯ CSS Pre-processors
 ◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing

? Choose a version of Vue.js that you want to start the project with 3.x
? Use class-style component syntax? No
? Use Babel alongside TypeScript (required for modern mode, auto-detected 
polyfills, transpiling JSX)? Yes
? Use history mode for router? (Requires proper server setup for index fallback 
in production) Yes
? Pick a linter / formatter config: Basic
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel, ESLint, etc.? In dedicated 
config files
? Save this as a preset for future projects? No


Vue CLI v5.0.8
✨  Creating project in /home/egs/tmp/vueapp.
🗃  Initializing git repository...
⚙️  Installing CLI plugins. This might take a while...

yarn install v1.22.19
info No lockfile found.
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...

success Saved lockfile.
Done in 19.80s.
🚀  Invoking generators...
📦  Installing additional dependencies...

yarn install v1.22.19
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...

success Saved lockfile.
Done in 23.14s.
⚓  Running completion hooks...

📄  Generating README.md...

🎉  Successfully created project vueapp.
👉  Get started with the following commands:

 $ cd vueapp
 $ yarn serve
```

### 查看 package.json 中相依套件及版本
```bash
$ cd vueapp

$ cat package.json 
{
  "name": "vueapp",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "core-js": "^3.8.3",
    "vue": "^3.2.13",
    "vue-router": "^4.0.3",
    "vuex": "^4.0.0"
  },
  "devDependencies": {
    "@typescript-eslint/eslint-plugin": "^5.4.0",
    "@typescript-eslint/parser": "^5.4.0",
    "@vue/cli-plugin-babel": "~5.0.0",
    "@vue/cli-plugin-eslint": "~5.0.0",
    "@vue/cli-plugin-router": "~5.0.0",
    "@vue/cli-plugin-typescript": "~5.0.0",
    "@vue/cli-plugin-vuex": "~5.0.0",
    "@vue/cli-service": "~5.0.0",
    "@vue/eslint-config-typescript": "^9.1.0",
    "eslint": "^7.32.0",
    "eslint-plugin-vue": "^8.0.3",
    "typescript": "~4.5.5"
  }
}
```

### 産生的預設程式結構

![2022-08-26 17-36-16 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/186875355-2c767476-c9a9-49ad-ba6a-e7156fe4af32.png)


### 執行由 CLI 産生的預設程式架構
```bash
$ yarn serve

yarn run v1.22.19
$ vue-cli-service serve
 INFO  Starting development server...


 DONE  Compiled successfully in 1436ms                                                                                                                                    下午4:10:07


  App running at:
  - Local:   http://localhost:8080/ 
  - Network: http://192.168.0.125:8080/

  Note that the development build is not optimized.
  To create a production build, run yarn build.
```


開啟瀏覽器，輸入 http://localhost:8080

![2022-08-26 17-34-07 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/186874978-3ee7fabf-fca3-4ac2-8408-1a8a93439f98.png)


```bash
$ git add . && git commit -m "Initial Commit" # 加入 git 新版本
$ code .    # 打開 vscode
```

## 使用 json-server 當作後端 Web Api
在我們的前端應用程式中會使用到後端伺服器的資料，方便起見直接使用 json-server　來當作後端 Web Api

### 安裝 json-server 
`$ npm install --save-dev json-server@0.17.0 npm-run-all@4.1.5`

### 建立給 json-server 使用的資料
```bash
$ touch data.json
# data.json 內容如下
{
  "products": [
    {
      "id": 1,
      "name": "kayak",
      "category": "Watersports",
      "description": "A boat form on person",
      "price": 275
    },
    {
      "id": 2,
      "name": "Lifejacket",
      "category": "Watersports",
      "description": "Protective and fashionable boat form on person",
      "price": 48.95
    },
    {
      "id": 3,
      "name": "Soccer Ball",
      "category": "Soccer",
      "description": "FIFA-approved size and weight",
      "price": 19.5
    },
    {
      "id": 4,
      "name": "Corner Flags",
      "category": "Soccer",
      "description": "Give your playing field a professional touch",
      "price": 34.95
    },
    {
      "id": 5,
      "name": "Stadium",
      "category": "Soccer",
      "description": "Flat-packed 35,000-seat stadium",
      "price": 79500
    },
    {
      "id": 6,
      "name": "Thinking Cap",
      "category": "Chess",
      "description": "Imporove brain efficiency by 75%",
      "price": 16
    },
    {
      "id": 7,
      "name": "Unsteady Chair",
      "category": "Chess",
      "description": "Secretly give your opponent a disadvantage",
      "price": 29.95
    },
    {
      "id": 8,
      "name": "Human Chess Board",
      "category": "Chess",
      "description": "A fun game for the family",
      "price": 75
    },
    {
      "id": 9,
      "name": "Bling Bling King",
      "category": "Chess",
      "description": "Gold-plated, diamond-studded King",
      "price": 1200
    }
  ],
  "orders": [
  ]
}
```

### 測試 json-server
```bash
$ npx json-server data.json -p 4600 # 使用 4600 port


  \{^_^}/ hi!

  Loading data.json
  Done

  Resources
  http://localhost:4600/products
  http://localhost:4600/orders

  Home
  http://localhost:4600

  Type s + enter at any time to create a snapshot of the database
GET /db 304 1.630 ms - -
GET /__rules 404 4.536 ms - 2
```

![image](https://user-images.githubusercontent.com/21993717/185843921-21bd33f3-7721-4b48-83f2-fc9d88757925.png)


![2022-08-22 13-12-47 的螢幕擷圖](https://user-images.githubusercontent.com/21993717/185844008-f153311c-1571-423f-91a2-17d9e5c54853.png)


### 設定 npm script in package.sjon
```json
// ...
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint",
    "json": "json-server data.json -p 4600",
    "start": "npm-run-all -p serve json"
  },
// ...
```

## 在專案中使用 Bootstrap CSS 套件
### 安裝 bootstrap 5
`$ npm install bootstrap@5.2.0`

### 引入 bootstrap in main.ts

```ts
// ./src/main.ts
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import "bootstrap/dist/css/bootstrap.css";

createApp(App).use(store).use(router).mount('#app')
```

<!-- ## 修改 tsconfig.json

將 "target" 改成 "es2015"

```json {linenos=table,hl_lines=[3]}
{
  "compilerOptions": {
    "target": "es2015",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,
    // ...
  }
}  
``` -->

## 為網站加入資料

### 建立資料模型
`$ mkdir ./src/data && touch ./src/data/entities.ts`

entities.ts 檔案內容如下：

```ts
// ./src/data/entities.ts
export class Product {
    constructor(
        public id: number,
        public name: string,
        public description: string,
        public category: string,
        public price: number) {}
}

export class OrderLine {
    constructor(public product: Product, public quantity: number) { }
    get total(): number {
        return this.product.price * this.quantity;
    }
}

export class Order {
    private lines: OrderLine[] = [];
    constructor(initialLines?: OrderLine[]) {
        if (initialLines) {
            this.lines.push(...initialLines);
        }
    }

    public addProduct(prod: Product, quantity: number) {
        const index = this.lines.findIndex(ol => ol.product.id === prod.id);
        if (index > -1) {
            if (quantity === 0) {
                this.removeProduct(prod.id);
            } else {
                this.lines[index].quantity += quantity;
            }
        } else {
            this.lines.push(new OrderLine(prod, quantity));
        }
    }
    
    public removeProduct(id: number) {
        this.lines = this.lines.filter(ol => ol.product.id !== id);
    }    

    get orderLines(): OrderLine[] {
        return this.lines;
    }

    get productCount(): number {
        return this.lines.reduce((total, ol) => total += ol.quantity, 0);
    }
    
    get total(): number {
        return this.lines.reduce((total, ol) => total += ol.total, 0);
    }
}
```


## 建立商品訂購相關元件
```bash
$ touch ./src/components/ProductItem.vue
$ touch ./src/components/CategoryList.vue
$ touch ./src/components/Header.vue
$ touch ./src/views/ProductList.vue
```
### 商品明細元件 (ProductItem)
```tsx
// ProductItem.vue
<template>
    <div class="card m-1 p-1 bg-light">
        <h4>
            {{ product?.name }}
            <span class="badge rounded-pill bg-primary float-end">
                ${{ product?.price.toFixed(2) }}
            </span>
        </h4>
        <div class="card-text bg-white p-1">
            {{ product?.description }}
            <button class="btn btn-success btn-sm float-end" @click="handleAddToCart">
                Add To Cart
            </button>
            <select class="from-control-inline float-end m-1" v-model.number="quantity">
                <option>1</option>
                <option>2</option>
                <option>3</option>
            </select>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import { Product } from "../data/entities";

export default defineComponent({
    name: "ProductItem",

    props: {
        product: {
            type: Object as PropType<Product>,
        },
    },

    data() {
        return {
            quantity: 1,
        };
    },

    methods: {
        handleAddToCart() {
            this.$emit("addToCart", {
                product: this.product,
                quantity: this.quantity,
            });
        },
    },
});
</script>
```

### 商品分類按鈕元件 (CategoryList.vue)
```tsx
// CategoryList.vue
<template>
    <div class="d-grid gap-2">
        <button
            v-for="c in categories"
            v-bind:key="c"
            v-bind:class="getButtonClasses(c)"
            @click="selectCategory(c)">
            {{ c }}
        </button>
    </div>
</template>
<script lang="ts">
import { defineComponent, PropType } from 'vue';


export default defineComponent({
    name: "CategoryList",
    props: {
        categories: {
            type: Object as PropType<string[]>,
            require: true
        },
        selected: {
            type: String as PropType<string>,
        },
    },

    methods: {
        selectCategory(category: string) {
            this.$emit("selectCategory", category);
        },
        getButtonClasses(category: string): string {
            const btnClass = this.selected === category ? "btn-primary" : "btn-secondary";
            return `btn ${btnClass}`;
        },
    },
});
</script>
```

### 表頭元件 (Header)
```tsx
// Header.vue
<template>
    <div class="p-1 bg-secondary text-white text-end">
        {{ displayText }}
        <!-- <button class="btn btn-sm btn-primary m-1>">Submit Order</button> -->
        <router-link to = "/order" class="btn btn-sm btn-primary m-1">
            Submit Order
        </router-link>
    </div>
</template>

<script lang="ts">
import { Order } from '@/data/entities';
import { defineComponent, PropType } from 'vue';

export default defineComponent({
    name: "Header",

    props: {
        order: {
            type: Object as PropType<Order>,
            required: true
        },
    },

    computed: {
        displayText(): string {
            const count = this.order.productCount;
            return count === 0 ? "(No Selection)" : `${count} Product(s), $${this.order.total.toFixed(2)}`;
        },
    },
});
</script>```

### 商品清單元件 (ProductList)
```tsx
// ./views/ProductList.vue
<template>
    <div>
        <Header v-bind:order="order" />
        <div class="container-fluid">
            <div class="row">
                <div class="col-3 p-2">
                    <CategoryList
                        v-bind:categories="categories"
                        v-bind:selected="selectedCategory"
                        @selectCategory="handleSelectCategory" 
                    />
                </div>
                <div class="col-9 p-2">
                    <ProductItem
                        v-for="p in filteredProducts"
                        v-bind:key="p.id"
                        v-bind:product="p"
                        @addToCart="handleAddToCart" 
                    />
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { Order, Product } from '@/data/entities';
import ProductItem from "../components/ProductItem.vue"
import CategoryList from "../components/CategoryList.vue"
import Header from "../components/Header.vue"

export default defineComponent({
    name: "ProductList",

    components: {
        ProductItem,
        CategoryList,
        Header
    },

    data() {
        const products: Product[] = [];
        [1, 2, 3, 4, 5].map((num) => 
            products.push(
                new Product(num, `Prod${num}`, `Product ${num}`, `Cat${num % 2}`, 100)));
        return {
            products,
            selectedCategory: "All",
            order: new Order()
        };
    },

    computed: {
        categories(): string[] {
            return ["All", ...new Set<string>(this.products.map((p) => p.category))];
        },
        filteredProducts(): Product[] {
            return this.products.filter((p) => this.selectedCategory == "All" || this.selectedCategory === p.category);
        }
    },

    methods: {
        handleSelectCategory(category: string) {
            this.selectedCategory = category;
        },
        handleAddToCart(data: {
            product: Product;
            quantity: number;
        }) {
            this.order.addProduct(data.product, data.quantity);
        },
    },
});
</script>
```

### 修改 App.vue

內容如下：

```tsx
// ./src/App.vue
<template>
  <ProductList />
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import ProductList from "./views/ProductList.vue"

export default defineComponent({
  name: "App",
  components: { ProductList },
});
</script>
```

### 檔案結構

![image](https://user-images.githubusercontent.com/21993717/186881951-87ad71bb-4741-41a1-bdc0-5ed1f2d4852c.png)


## 執行程式
```bash
$ npm start

 ERROR  Failed to compile with 1 error                                                       下午6:39:49

[eslint] 
/home/egs/cal-data/tech-test/typescript/book/vueapp/src/components/Header.vue
  17:11  error  Component name "Header" should always be multi-word  vue/multi-word-component-names

✖ 1 problem (1 error, 0 warnings)


You may use special comments to disable some warnings.
Use // eslint-disable-next-line to ignore the next line.
Use /* eslint-disable */ to ignore all warnings in a file.
ERROR in [eslint] 
/home/egs/cal-data/tech-test/typescript/book/vueapp/src/components/Header.vue
  17:11  error  Component name "Header" should always be multi-word  vue/multi-word-component-names

✖ 1 problem (1 error, 0 warnings)


webpack compiled with 1 error
```

執行時發生錯誤，由訊息中可看出是因為 Header.vue 這個元件的命名所導致。esline 預設的命名檢核規則必須是要超過一個英文字(Word)。解決方法有二：一是將 Header.vue 改名為二個字，如FunHeader.vue。第二個方法是修改 esline 中的檢核規則，避掉 multi word naming 的檢核。

加入第十七行的設定

```js　{linenos=table,hl_lines=[17]}
module.exports = {
  root: true,
  env: {
    node: true
  },
  'extends': [
    'plugin:vue/vue3-essential',
    'eslint:recommended',
    '@vue/typescript/recommended'
  ],
  parserOptions: {
    ecmaVersion: 2020
  },
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'vue/multi-word-component-names': 0 // 避掉 compoenent 必須是要 Multi word Name
  }
}
```

![image](https://user-images.githubusercontent.com/21993717/186888055-a95eb892-cc18-475c-93f6-a761014fa847.png)

![image](https://user-images.githubusercontent.com/21993717/186888104-174f32a6-89c8-4627-b821-6f16d29e06e8.png)


## 使用 Data Store 來管理 Vue 應用程式的資料
在大多數的 Vue 專案中，應用程式的資料是交由 Vuex 套件來管理。它提供　"data store" 功能已經整合到 Vue.js　的 API 內。

### 安裝 Vuex
在本筆記開頭，使用 vue/cli 建立專案時，我們已選擇使用 vuex 選項，所以在 package.json 中已 有 vuex 套件的安裝資訊。若在建立專案時未選擇使用 vuex，則須自行手動安裝 vuex 套件。

```bash
$ npm install vuex@4.0.0
```

### 建立 data store
```typescript
// ./src/data/index.ts
import { Order, Product } from '@/data/entities'
import { createStore } from 'vuex'

export interface StoreState {
  products: Product[],
  order: Order,
  selectedCategory: string,
}

type ProductSelection = {
  product: Product,
  quantity: number
}

export default createStore<StoreState>({
  state: {
    products: [1, 2, 3, 4, 5].map(num => new Product(
      num, 
      `StoreProd${num}`, 
      `Product${num}`, 
      `Cat${num % 2}`
      , 450)
    ),
    order: new Order(),
    selectedCategory: "All",
  },

  mutations: {
    selectCategory(currentState: StoreState, category: string) {
      currentState.selectedCategory = category;
    },
    addToOrder(currentState: StoreState, selection: ProductSelection) {
      currentState.order.addProduct(selection.product, selection.quantity);
    }
  },

  getters: {
    categories(state): string[] {
      return ["All", ...new Set(state.products.map(p => p.category))];
    },
    filteredProducts(state): Product[] {
      return state.products.filter(p => state.selectedCategory === "All" || state.selectedCategory === p.category);
    }
  },

  actions: {
  },

  modules: {
  }
})
```

### 將元件連結至 data store

修改 Header.vue 內容如下：

```tsx
// ./src/components/Header.vue
<template>
    <div class="p-1 bg-secondary text-white text-end">
        {{ displayText }}
        <button class="btn btn-sm btn-primary m-1>">Submit Order</button> 
    </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import { useStore } from "vuex";

export default defineComponent({
    name: "Header",

    setup() {
        return {store: useStore()};
    },

    computed: {
        displayText(): string {
            // const count = this.order.productCount;
            // return count === 0 ? "(No Selection)" : `${count} Product(s), $${this.order.total.toFixed(2)}`;
            const count = this.store.state.order.productCount;
            return count === 0 ? "(No Selection)" : `${count} Product(s) ` + `$${this.store.state.order.total.toFixed(2)}`;
        },
    },
});
</script>
```

修改　ProductList.vue　如下：

```tsx
// ./src/views/ProductList.vue
<template>
    <div>
        <Header v-bind:order="order" />
        <div class="container-fluid">
            <div class="row">
                <div class="col-3 p-2">
                    <CategoryList
                        v-bind:categories="categories"
                        v-bind:selected="selectedCategory"
                        @selectCategory="handleSelectCategory" 
                    />
                </div>
                <div class="col-9 p-2">
                    <ProductItem
                        v-for="p in filteredProducts"
                        v-bind:key="p.id"
                        v-bind:product="p"
                        @addToCart="handleAddToCart" 
                    />
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { Order, Product } from '@/data/entities';
import { defineComponent } from 'vue';
import ProductItem from "../components/ProductItem.vue"
import CategoryList from "../components/CategoryList.vue"
import Header from "../components/Header.vue"

import { mapMutations, mapState, mapGetters } from 'vuex';
import { StoreState } from "../store";

export default defineComponent({
    name: "ProductList",
    components: {
        ProductItem,
        CategoryList,
        Header
    },
    data() {
        const products: Product[] = [];
        [1, 2, 3, 4, 5].map((num) => 
            products.push(
                new Product(num, `Prod${num}`, `Product ${num}`, `Cat${num % 2}`, 100)));
        return {
            products,
            selectedCategory: "All",
            order: new Order()
        };
    },
    computed: {
        ... mapState<StoreState>({
            selectedCategory: (state: StoreState) => state.selectedCategory,
            products: (state: StoreState) => state.products,
            order: (state: StoreState) => state.order,
        }),
        ...mapGetters(["filteredProducts", "categories"]),
    },

    methods: {
        ...mapMutations({
            handleSelectCategory: "selectCategory",
            handleAddToCart: "addToOrder",
        }),
    },
});
</script>
```
### 使用 data store 的測試結果

![image](https://user-images.githubusercontent.com/21993717/187012958-bb263fa4-3ec2-489f-9933-f4483abea9fe.png)


## 修改程式改由網路服務取得資料

### 修改 data store 程式內容
```typescript
import { Order, Product } from '@/data/entities'
import { createStore } from 'vuex'

export interface StoreState {
  products: Product[],
  order: Order,
  selectedCategory: string,
  storedId: number
}

type ProductSelection = {
  product: Product,
  quantity: number
}

export default createStore<StoreState>({
  state: {
    products: [1, 2, 3, 4, 5].map(num => new Product(
      num, 
      `StoreProd${num}`, 
      `Product${num}`, 
      `Cat${num % 2}`
      , 450)
    ),
    order: new Order(),
    selectedCategory: "All",
    storedId: -1
  },

  mutations: {
    selectCategory(currentState: StoreState, category: string) {
      currentState.selectedCategory = category;
    },
    addToOrder(currentState: StoreState, selection: ProductSelection) {
      currentState.order.addProduct(selection.product, selection.quantity);
    },
    addProducts(currentState: StoreState, products: Product[]) {
      currentState.products = products;
    },
    setOrderId(currentState: StoreState, id: number) {
      currentState.storedId = id;
    },
    resetOrder(currentState: StoreState) {
       currentState.order = new Order();
    }
  },

  getters: {
    categories(state): string[] {
      return ["All", ...new Set(state.products.map(p => p.category))];
    },
    filteredProducts(state): Product[] {
      return state.products.filter(p => state.selectedCategory === "All" || state.selectedCategory === p.category);
    }
  },

  actions: {
    async loadProducts(context, task: () => Promise<Product[]>) {
      const data = await task();
      context.commit("addProducts", data);
    },
    async storeOrder(context, task: (order: Order) => Promise<number>) {
      context.commit("setOrderId", await task(context.state.order));
      context.commit("resetOrder");
    },
  },

  modules: {
  }
})
```

### 建立 httpHandler.ts

在撰寫 httpHandler.ts 前要安裝 axios

```bash
$ npm install axios@0.27.2
```

```bash
$ touch ./src/data/httpHandler.ts
```
httpHandler.ts 內容如下：

```typescript
import Axios from "axios";
import { Order, Product } from "./entities";

const protocol = "http";
const hostname = "localhost";
const port = 4600;
const urls ={
    products: `${protocol}://${hostname}:${port}/products`,
    orders: `${protocol}://${hostname}:${port}/orders`
};

export class HttpHandler {
    loadProducts(): Promise<Product[]> {
        return Axios.get<Product[]>(urls.products).then(response => response.data);
    }

    storeOrder(order: Order): Promise<number> {
        const orderData = {
            lines: [...order.orderLines.values()].map(ol => ({
                productId: ol.product.id,
                productName: ol.product.name,
                quantity: ol.quantity
            }))
        }
        return Axios.post<{id: number }>(urls.orders, orderData).then(response => response.data.id);
    }
}
```

## 修改 App.vue 從網路載入商品資料

```tsx
<template>
  <ProductList />
</template>

<script lang="ts">
import { defineComponent, onMounted } from 'vue';
import { useStore } from 'vuex';
import { HttpHandler } from './data/httpHandler';
import ProductList from "./views/ProductList.vue"

export default defineComponent({
  name: "App",
  components: { ProductList },

  setup() {
    const store = useStore();
    const handler = new HttpHandler();
    onMounted(() => store.dispatch("loadProducts", handler.loadProducts));
  },
});
</script>
```

onMounted() 是 Vue.js 提供的一個元件生命週期函式。通常都會被選定在這個時機來載入外部資料。

## 執行測試

程式至此已將資料連結由 local 改到 Json-Server，並將取得的資料放入 data store 中，且元件資料綁定至 data store。

```bash
$ npm start
```
![image](https://user-images.githubusercontent.com/21993717/187013237-0d2ffdac-e34c-47ed-8497-bfe11ad42a2d.png)


## 設定 URL 路由
雖然在筆記最前面使用 @vue/cli 來産生應用程式預設架構時，我們已選擇使用 Router 功能，但程式至此我們還沒有使用到 Router 相關功能。我們先將 App.vue `template` 中｀寫死｀使用 ProductList 元件的語法改成透過 router 功能來呼叫 ProductList 元件。

### 修改 Router 設定資料
修改 ./src/router/index.ts 檔案，將預設産生的內容修改如下
```ts
import ProductList from '@/views/ProductList.vue'
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'

const routes: Array<RouteRecordRaw> = [
  {
    path: '/products',
    component: ProductList
  },
  {
    path: '/',
    redirect: '/products'
    }
]

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
})

export default router
```

### 修改 App.vue 
 將 App.vue 中 `template` 寫死的程式碼（使用元件）改成 <router-view> tag

```tsx
// ./src/App.vue
<template>
  <router-view/>
</template>

<script lang="ts">
import { defineComponent, onMounted } from 'vue';
import { useStore } from 'vuex';
import { HttpHandler } from './data/httpHandler';
// import ProductList from "./views/ProductList.vue"

export default defineComponent({
  name: "App",
  // components: { ProductList },

  setup() {
    const store = useStore();
    const handler = new HttpHandler();
    onMounted(() => store.dispatch("loadProducts", handler.loadProducts));
  },
});
</script>
```

### 執行程式

修改後執行結果：

![image](https://user-images.githubusercontent.com/21993717/187056461-ea3ec707-5ca2-4cc3-be52-3e08085f3a10.png)


## 完成其他功能
接下來我們要完成其餘的二個功能：訂單確認功能及訂購成功等功能，並將此二功能也加入 URL路由 設定檔中。

### 加入訂單確認元件
`$ touch ./src/views/OrderDetails.vue`

```tsx
<template>
    <div>
        <h3 class="text-center bg-primary text-white p-2">
            Order Summary
        </h3>
        <div class="p-3">
            <table class="table table-sm table-striped">
                <thead>
                    <tr>
                        <th> Quantity</th>
                        <th> Product</th>
                        <th class="text-end"> Price</th>
                        <th class="text-end"> Subtotal</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="line in order.lines" v-bind:key="line.product.id">
                        <td>{{ line.quantity }}</td>
                        <td>{{ line.product.name }}</td>
                        <td class="text-end">{{ line.product.price.toFixed(2) }}</td>
                        <td class="text-end">{{ line.total.toFixed(2) }}</td>
                    </tr>
                </tbody>
                <tfoot>
                    <tr>
                        <th class="text-end" colspan="3">Total:</th>
                        <th class="text-end">${{ order.total.toFixed(2) }}</th>
                    </tr>
                </tfoot>
            </table>
        </div>
        <div class="text-center">
            <router-link to="/products" class="btn btn-secondary m-1">
                Back
            </router-link>
            <button class="btn btn-primary m-1" @click="submit">Submit Order</button>
        </div>
    </div>
</template>

<script lang="ts">
import { Order } from '@/data/entities';
import { HttpHandler } from '../data/httpHandler';
import { StoreState } from '@/store';
import { defineComponent } from 'vue';
import { mapActions, mapState } from 'vuex';


export default defineComponent({
    name: "OrderDetails",

    computed: {
        ...mapState<StoreState>({
            order: (state: StoreState) => state.order,
        }),
    },

    methods: {
        ...mapActions(["storeOrder"]),
        submit() {
            this.storeOrder((order: Order) => {
                return new HttpHandler().storeOrder(order).then((id) => {
                    this.$router.push("/summary");
                    return id;
                });
            });
        },
    },
});
</script>
```

### 加入訂購成功元件

`$ touch ./src/views/Summary.vue`

```tsx
<template>
    <div class="m-2 text-center">
        <h2>Thanks!</h2>
        <p>Thanks for placing your order.</p>
        <p>Your order is #{{ id }}</p>
        <p>We'll ship your goods as soon as possible.</p>
        <router-link to="/products" class="btn btn-primary">OK</router-link>
    </div>
</template>

<script lang="ts">
import { StoreState } from '@/store';
import { defineComponent } from 'vue';
import { mapState } from 'vuex';

export default defineComponent({
    name: "Summary",

    computed: {
        ...mapState<StoreState>({
            id: (state: StoreState) => state.storedId,
        })
    }
})
</script>
```

### 修改 Header.vue URL 轉址功能
```tsx
// Header.vue
<template>
    <div class="p-1 bg-secondary text-white text-end">
        {{ displayText }}
        <!-- <button class="btn btn-sm btn-primary m-1>">Submit Order</button> -->
        <router-link to = "/order" class="btn btn-sm btn-primary m-1">
            Submit Order
        </router-link>
    </div>
</template>

<script lang="ts">
// import { Order } from '@/data/entities';
import { defineComponent, PropType } from 'vue';
import { useStore } from "vuex";

export default defineComponent({
    name: "Header",

    // props: {
    //     order: {
    //         type: Object as PropType<Order>,
    //         required: true
    //     },
    // },

    setup() {
        return {store: useStore()};
    },

    computed: {
        displayText(): string {
            // const count = this.order.productCount;
            // return count === 0 ? "(No Selection)" : `${count} Product(s), $${this.order.total.toFixed(2)}`;
            const count = this.store.state.order.productCount;
            return count === 0 ? "(No Selection)" : `${count} Product(s) ` + `$${this.store.state.order.total.toFixed(2)}`;
        },
    },
});
</script>
```

### 完成路由設定
```tsx
// ./src/router/index.ts
import ProductList from '@/views/ProductList.vue'
import OrderDetails from '@/views/OrderDetails.vue'
import Summary from '@/views/Summary.vue'
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'

const routes: Array<RouteRecordRaw> = [
  {
    path: '/products',
    component: ProductList
  },
  {
    path: '/order',
    component: OrderDetails
  },
  {
    path: '/summary',
    component: Summary
  },
  {
    path: '/',
    redirect: '/products'
    }
]

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
})

export default router
```

### 執行測試

![image](https://user-images.githubusercontent.com/21993717/187056685-9c3dd9bc-596c-400e-ac9d-ae59c305c484.png)

![image](https://user-images.githubusercontent.com/21993717/187056696-457bb66a-20d2-4c3d-8be0-1865c56b17ea.png)

![image](https://user-images.githubusercontent.com/21993717/187056749-0a09c522-0bca-4e60-9aa9-5c15743eaba3.png)



## 佈署應用程式
使用 NodeJS & Express 來當作 Http Server & Web Api Server

### 安裝 express 套件 & -connect-history-api-fallback 套件

`$ npm install --save-dev express@4.18.1 connect-history-api-fallback@2.0.0`

### 建立 node 伺服器
`$ touch server.js`

```js
const express = require("express");
const jsonServer = require("json-server");
const history = require("connect-history-api-fallback");
const app = express();

app.use(history());
app.use("/", express.static("dist"));

const router = jsonServer.router("data.json");
app.use(jsonServer.bodyParser);
app.use("/api", (req, res, next) => router(req, res, next));

const port = process.argv[3] || 4003;
app.listen(port, ()=> console.log(`Running on port ${port}`));
```

### 修改 url 路徑
修改 httpHandler.ts
```typescript
import Axios from "axios";
import { Order, Product } from "./entities";

// const protocol = "http";
// const hostname = "localhost";
// const port = 4600;
// const urls = {
//     products: `${protocol}://${hostname}:${port}/products`,
//     orders: `${protocol}://${hostname}:${port}/orders`,
// };
const urls = {
    products: "/api/products",
    orders: "/api/orders"
}

export class HttpHandler {
    loadProducts(callback: (producs: Product[]) => void): void {
        Axios.get(urls.products).then(response => callback(response.data))        
    }

    storeOrder(order: Order, callback: (id: number) => void): void {
        let orderData = {
            lines: [...order.orderLines.values()].map(ol => ({
                productId: ol.product.id,
                productName: ol.product.name,
                quantity: ol.quantity
            }))
        }
        Axios.post(urls.orders, orderData).then(response => callback(response.data.id));
    }
}
```

### 建置 Vue App
```bash
$ npm run build

npm run build

> vueapp@0.1.0 build
> vue-cli-service build

All browser targets in the browserslist configuration have supported ES module.
Therefore we do not build two separate bundles for differential loading.


⠧  Building for production...

 WARNING  Compiled with 3 warnings                                                          下午12:03:12

[eslint] 
/home/egs/cal-data/tech-test/typescript/book/vueapp/src/components/Header.vue
  13:27  warning  'PropType' is defined but never used  @typescript-eslint/no-unused-vars

✖ 1 problem (0 errors, 1 warning)


You may use special comments to disable some warnings.
Use // eslint-disable-next-line to ignore the next line.
Use /* eslint-disable */ to ignore all warnings in a file.
 warning  

entrypoint size limit: The following entrypoint(s) combined asset size exceeds the recommended limit (244 KiB). This can impact web performance.
Entrypoints:
  app (336 KiB)
      css/chunk-vendors.2186f2e7.css
      js/chunk-vendors.c958b33a.js
      js/app.e01e1595.js


 warning  

webpack performance recommendations: 
You can limit the size of your bundles by using import() or require.ensure to lazy load some parts of your application.
For more info visit https://webpack.js.org/guides/code-splitting/

  File                                   Size                          Gzipped

  dist/js/chunk-vendors.c958b33a.js      139.69 KiB                    49.73 KiB
  dist/js/app.e01e1595.js                9.58 KiB                      3.57 KiB
  dist/css/chunk-vendors.2186f2e7.css    187.06 KiB                    26.69 KiB

  Images and other types of assets omitted.
  Build at: 2022-08-28T04:03:12.312Z - Hash: b0905e88b215d52d - Time: 9430ms

 DONE  Build complete. The dist directory is ready to be deployed.
 INFO  Check out deployment instructions at https://cli.vuejs.org/guide/deployment.html
 ```

### 執行
```bash
$ node server.js

Running on port 4003
```

![image](https://user-images.githubusercontent.com/21993717/187056943-89198e60-57ec-413c-ac9a-8e85c78a3c5b.png)

功能完成後的程式檔案結構：

![image](https://user-images.githubusercontent.com/21993717/187056995-f6b318d6-22cf-4106-ad1d-1067364528d3.png)
