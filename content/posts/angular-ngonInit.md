---
title: "關於 Angular中的 constructor 與 ngOnInit"
date: 2022-01-02
description: "在本篇筆記中來紀錄一下 Angular中的 constructor 與 ngOnInit 有何不同的使用情境，另也探討 ngOnInit 與 ngOnChanges 有何不同"
tags: ["angular", "rxjs", "life cycle"]
categories: ["angular"]
---

# Angular中的 constructor 與 ngOnInit

在本篇筆記中來紀錄一下 Angular中的 constructor 與 ngOnInit 有何不同的使用情境，也探討一下 ngOnInit() 的非必要性。


## ngOnInit 與 constructor

ngOnInit() 與 Class 中的其他 Method 無異，僅因為它剛好被命名為 ngOnInit，常會被與 constructor 搞混用途。其實它並非是必要的，完全取決於你。當 Angular 編譯器進行程式編譯時會將是否 Class 中有實現 ngOnInit() 的方法記錄在特定的 Flag 中，這個 Flag 會在 "變更檢測"時決定是不是要呼叫 ngOnInit() 這個方法。

而 constructor 則完全是另外的一件事，不管你有沒有在類別中實現它，在建立類別實例時它都會被呼叫到。

Angular啟動時的二個主要階段：
- 建構元件樹(component tree)
- 執行變更檢測(change detection)

constructor 是在第一個階段被呼叫的，這就是為什麼我們不能將任何依賴於 DOM、元素、輸入數據、綁定等的初始化邏輯放在constructor— 因為它們尚不可使用。
而 ngOnInit 則在第二階段，實際上所有的生命週期鉤子都是“變更檢測”階段的一部份。

當 Angular 開始變更檢測時，組件樹已被構建，樹中所有組件的構造函數都被調用。它已經完成了組件 DOM 的創建，通過構造函數注入了所有必需的依賴項並處理了輸入綁定。每個組件的模板節點都被添加到 DOM 中。 也可以取得初始化組件可能需要的所有資料，如：DI Provider、DOM 和輸入綁定(input bindings)。

在實際的使用案例上 constructor 最常被使用的是用來“注入依賴項目”(當然不僅限於此)


## ngOnInit 是否為必須

在之前使用 angular cli(如：14.0.6) 産生 component 會發現 component class 程式碼中是預設會使用到 ngOnInit 的。但在新版的 angular cli 中(如：15.0.4) 産生的程式架構中已預設不用使用 ngOnInit (與constructor) 了。

`angular cli 14.0.6`

![v13ComponentClass](https://user-images.githubusercontent.com/21993717/210241077-61b92621-3aeb-4595-9be4-02ef2a9425ac.png)

`angular cli 15.0.4`

![v15 0 4ComponentClass](https://user-images.githubusercontent.com/21993717/210241108-46594182-a3c6-454b-b39e-a0f0bfeb57da.png)



其實：
  - 在處理 Observables 時你不需要 ngOnInit
  - 大多數時候，ngOnChanges 是 ngOnInit 的更好選擇
  
以下就要來說說如何不使用 ngOnInit 的一些寫法。

常見的例子是 Angular 使用 Fetch 來讀取後端 API 資料顯示在畫面，寫法如下：

```ts
data: MyData;

constructor(private readonly dataService: DataService) { }

ngOnInit() {
  this.dataService.getData().subscribe(
    data => this.data = data,	//透過訂閱取得 data 後利用資料綁定的功能將資料顯示在模版中
  );
}
```

更完善的寫法是還要包含有取消訂閱的程式碼，如下：

```ts
data: MyData;

private readonly onDestroy = new Subject();

ngOnInit() {
  this.dataService.getData()
    .pipe(
      takeUntil(this.onDestroy)
    ).subscribe(
      data => this.data = data,
    );
}

ngOnDestroy() {
  this.onDestroy.next();
  this.onDestroy.complete();
}
```


## Observable 與 async pipe

但其實在 Angular 中，模板裡不僅限於使用靜態值。我們可以直接在模板中使用 Observables。換句話說，很少需要在元件類別(component class)中訂閱的 Observable。實際上需要值的是模板(template)，所以我們應該在那裡訂閱。

更好的作法是在模版中使用 AysncPipe，它用來訂閱 Observable 並監聽取得回傳值，並且它還負責在元件銷毀時取消訂閱。

```ts
@Component({...})
export class Component {
  
  readonly data$ = this.dataService.getData();
	
  constructor(private readonly dataService: DataService) { }
}
```

在模版中使用 async pipe 來訂閱 data$ 這個 Observable 並取得回傳值來顯示在畫面中。使用這樣的寫法可以清楚的看出來取得資料的邏輯其實不須要存在元件類別中的。

```html
<p> {{ (data$ | async).name }} </p>
```

另一個範例：

```ts
data?: { name: string, time: Date };

constructor(private readonly dataService: DataService) { }

ngOnInit() {
  this.dataService.getData().subscribe(
    response => {
      const first = response.data[0];
      
      this.data = {
        name: first.name,
        time: new Date(first.time)
      }
    }
  );
}
```

可重構成：

```ts
readonly data$ = this.dataService.getData().pipe(
  map((response) => {
    const first = response.data[0];
    
    return {
      name: response.name,
      time: new Date(response.time)
    }
  }),
);

constructor(private readonly dataService: DataService) { }
```

由上述二個例子了解，只須多利用 RxJs 的各種 Operator 與 async pipe，即可減少使用 ngOnInit hook 的使用了。


## 嵌套 Observables 的較佳寫法

在使用後端資料時，常會有一種使用情境是呼收 API 時需要由 routing 中取得資料（如：ID）例如

```ts
hero?: { name: string };

constructor(
  private readonly route: ActivatedRoute,
  private readonly heroService: HeroService,
) { }

ngOnInit() {
  this.route.params.subscribe(
    (params) = > {
      const id = params.id;

      this.heroService.getHero(id).subscribe(
        response => this.hero = response
      );
    }
  )	
}
```

在這個範例中看到二個問題：
 - 嵌套 Observables 的寫法
 - 使用 ngOnInit 的必要性

程式碼重構如下：

```ts
readonly hero$ = this.route.params.pipe(
  switchMap(params => this.heroService.getHero(params.id))
);

constructor(
  private readonly route: ActivatedRoute,
  private readonly heroService: HeroService,
) { }
```

```html
<p> {{ (hero$ | async).name }} </p>
```

可使用 Observables 寫法免除在 ngOnInit hook 中多餘的程式碼 與 使用 switchMap operator 來消除嵌套 Observables 呼叫方式。


## 如何解決模板中過多的 async 使用

問題來了，當在模板中使用了過多的 async，除了混淆模板外甚至會産生一些性能的問題。

在許多 element 中使用了單一個 Observable，如：
```html
<p> {{ (hero$ | async).name }} </p>
<p> {{ (hero$ | async).lastname }} </p>
<p> {{ (hero$ | async).city }} </p>
```

解決這個問題可善用 NgIf 這個“指令“，代碼如下：
```html
<ng-container *ngIf="hero$ | async as hero">
  <p> {{ hero.name }} </p>
  <p> {{ hero.surname }} </p>
  <p> {{ hero.city }} </p>
</ng-container>
```
其中透過 *ngIf 指令中的 as 用法，使得我們在 p 元素中去綁定 hero 這個變數而不是 (hero$ | async)，使用這種技術來保存從 `AsyncPIpe` 解析出來的值，並在僅訂閱一次的情況下在許多元素中去使用它。
另外這段另一個 Angular 指令： ng-container，ng-container這個指令在實際産出時不會有任何額外的元素在 DOM 中。

## 善用 RxJs Operator 來解決 component 中使用到許多的 Observable 的問題

如下範例：
```ts
readonly hero$ = this.route.params.pipe(
  switchMap(params => this.heroService.getHero(params.id))
);

readonly pet$ = this.route.params.pipe(
  switchMap(params => this.heroService.getPet(params.id))
);

readonly cities$ = this.heroService.getCities();

constructor(
  private readonly route: ActivatedRoute,
  private readonly heroService: HeroService,
) { }
```

可重構成：
```ts
readonly vm$ = combineLatest([
  this.route.params.pipe(
    switchMap(params => this.heroService.getHero(params.id))
  ),
  this.route.params.pipe(
    switchMap(params => this.heroService.getPet(params.id))
  ),
  this.heroService.getCities(),
]).pipe(
  map(([hero, pet, cities]) => {
    return {
      hero,
      pet,
      cities
    }
  })
);


constructor(
  private readonly route: ActivatedRoute,
  private readonly heroService: HeroService,
) { }
```

首先，只有當 route 參數産生變化時才分別發出 getHero、getPet與 getCities 的 fetch request，同時透過 combineLatest operator 在所有的 request 都取回資料後來産生一個 Observable。
第二是將陣列資料 mapping 到具有屬性名稱的物件中。
這使得我們的模版變得簡潔了許多

```html
<ng-container *ngIf="vm$ | async as vm">
  <p> {{ vm.hero.name }} </p>
  <p> {{ vm.hero.surname }} </p>
  <p> {{ vm.hero.city }} </p>
  
  <p> {{ vm.pet.name }} </p>
  
  <ul>
    <li *ngFor="let city of vm.cities"> {{ city }} </li>
  </ul>
</ng-container>
```

## ngOnInit 與 ngOnChanges

有時我們可能會因為誤用 ngOnInit 而産生一些 bug，特別是在一些包含有子元件的父元件中，讓我們來看看範例：

```ts
@Component({
  template: `<p> {{ fullName }} </p>`,
})
export class NameComponent implements OnInit {
  @Input() name: string;
  @Input() lastname: string;

  fullName: string;

  ngOnInit() {
    this.fullName = `${this.name} ${this.lastname}`;
  }
}
```

這樣的程式碼在開始時是可正常工作的，但一旦 input 值有更動時就不會更新資料。

那麼如何在每次輸入值有更動時要能正確的顯示最新資料呢？有兩種方法：
 - 使用適合的 Hook，所以要改用 ngOnChanges
 - 使用 setters 技術


每次輸入更改時 ngOnChanges 會被運行，因此我們可以使用這個 hook 根據輸入資料更新我們的內部狀態。

```ts
@Component({
  template: `<p> {{ fullName }} </p>`,
})
export class NameComponent implements OnChanges {
  @Input() name: string;
  @Input() surname: string;

  fullName: string;

  ngOnChanges() {
    this.fullName = `${this.name} ${this.surname}`;
  }
}
```

在第一次 ngOnChanges 被呼叫(由 input 資料更新而觸發)後，會緊隨著呼叫 ngOnInit，但僅限一次，ngOnChanges 資料的再更新而被再次的觸發，但 ngOnInit 則不會了。
除此不同外，ngOnChanges 傳入的資料是一個相對完整的　SimpleChanges 參數，它提供了當前和先前的狀態值。

如果你要處理的資料邏輯是如此簡單，其實可以採用相對簡易的 getters 方式來完成，如：
```ts
@Component({
  template: `<p> {{ fullName }} </p>`,
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class NameComponent {
  @Input() name: string;
  @Input() surname: string;

  get fullName(): string {
    return `${this.name} ${this.surname}`
  }
}
```

當然也可採用 Setters 的方式，如：
```ts
Copy
interface MyDTO {
  data: {
    name: string;
    time: string;
  }[]
}

@Component({
  template: `<p> {{ time }} </p>`,
})
export class TimeComponent {
  @Input() 
  set vm(value: MyDTO) {
    const first = value.data[0];
   
    this.time = new Date(first.time);
  }

  time: Date;
}
```

採用 Getters 或 Setters 的方式就可以不用管到生命週期鉤子的相關技術。如果你要的結果不會依賴一個以上的 input value，那麼這種技術是很好用的。