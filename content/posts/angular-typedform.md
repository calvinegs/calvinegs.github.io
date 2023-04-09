---
title: "Typed Form in angular 15"
date: 2023-04-09
description: "本篇筆記將要紀錄如何使 Angular 15 Typed Form"
tags: ["angular", "Typedform"]
categories: ["angular"]
---

# Typed Form in angular

從 Angular 14 開始，Angular 為 Reactive Forms 添加了完整的內建`類型安全`功能，FormGroup、FormControl、FormArray、FormBuilder 皆預設具備有型別，好處當然就是在開發階段就可以避免一些因為型別而産生的問題錯誤。

而原有在 Angular 13 中的 FormGroup、FormControl、FormArray、FormBuilder被改名成 UntypedFormGroup、UntypedFormControl、UnTypedFormArray、UnTypedFormBuilder，若有需要使用到跨型別表單的用法，可以透過這些原有的功能來完成。

使用這些內建 Typeed form 功能除了獲得額外的型別安全好處，更令人高興的是無需對原程式進行任何代碼更改，就來看看要如何取得這些優點。

以下面的 login Compoenet 為例，和舊版的程式相同，使用 FormBuilder API 來宣告 email 和 password 兩個表單欄位

```ts {linenos=table,hl_lines=[]}
@Component({
  selector: 'login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.css']
})
export class LoginComponent {

   loginForm = this.fb.group({
      email: ['', {
        validators: [Validators.required, Validators.email]
      }],
      password: ['', [Validators.required, Validators.minLength(8)]]
   });

  constructor(private fb: FormBuilder) {

  }

  onLogin() {

  }
}
```

## Typed form 好處

### auto-completion 

當你要輸入以下程式碼時

```ts
    const email = this.loginForm.value.email;
```

在 `this.loginform.value.` 後面 VSCode 就自動顯示表單所有的欄位供你選擇，如下圖：

![image](https://user-images.githubusercontent.com/21993717/230767051-2565d0e7-74a0-42cd-aed9-a785943a1b73.png)

### 建議提示


### 欄位值給定不正確值(錯誤型態)時也不會從編譯器獲得有用的錯誤訊息

檔你輸入不正確的欄位值時，VSCode 也會得到有用的編譯器錯誤消息，以防我們將錯誤類型的值傳遞給特定的表單欄位。如：給 email 欄位指定“數值”

```ts
    this.loginForm.patchValue({
      email: 21
    })
```

vSCode 告訴我們不能把數值指定給 `字串欄位`

![image](https://user-images.githubusercontent.com/21993717/230767782-1257ae73-348b-440a-b7ec-33ec288863d0.png)



## 避免使用陷阱

以下程式看似平常，宣告一個 FormGroup 的變數，然後在 ngOnInit 中去初始化它。當這樣的使用方式不再建議使用，因為它會阻止您享用類型安全的好處。

```ts {linenos=table,hl_lines=["12-18"]}
@Component({
  selector: 'login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.css']
})
export class LoginComponent implements OnInit {

  constructor(private fb: FormBuilder) {
  }

  ngOnInit() {
    loginForm: FormGroup = this.fb.group({
      email: ["", {
        validators: [Validators.required, Validators.email]
      }],
      password: ['', [Validators.required, Validators.minLength(8)]]
    });
  }
}
```

你會發現在存取 form value 時不僅 auto-completion 的支援功能不見了，在使用 patchValue 方法時，也不再有欄位名稱的建議提示，欄位值給定不正確值(錯誤型態)時也不會從編譯器獲得有用的錯誤訊息。

原因很簡單：因為我們將表單宣告為 FormGroup 類型，它隱式默認為 `FormGroup<any>`，這意味著你宣告的表單值將是 any 類型。眾所皆知的，any 本質上意味著編譯器沒有關於表單值的任何類型訊息，因此所有`類型安全`都被關閉，表單不再是類型安全的。

![image](https://user-images.githubusercontent.com/21993717/230768044-128e2bb5-d675-4d85-80ee-37079bce6694.png)

因為只要善用 typescript 推論功能，不必特地去為 loginForm 指定為 FormGroup 型別。如果一定要，也就須一併為它宣告成“自定型別” － `loginForm: FormGroup<T>`

## 使用或不使用 空值 null

預設的情況下，所有表單欄位都被認為是可以為空值的。這是因為當我們呼叫表單 reset() 方法時，我們通常希望 Angular 會將所有表單欄位設置為 null 值。當然可以選擇將表單欄位宣告為不可為空值，這意味著表單欄位將重置為其初始值而不是 null。

下面的程式碼表示，欄位 email 不可以為空值： （透過 nonNullable 語法）

```ts {linenos=table,hl_lines=[2]}
form = this.fb.group({
  email: this.fb.nonNullable.control("", {
    validators: [Validators.required, Validators.email],
  }),
  password: ['', [Validators.required, Validators.minLength(8)]]
});
```

如果想要所有在 form group 中的欄位皆不可為 null 值，可以透過 `fb.nonNullable` 的語法來達成

```ts {linenos=table,hl_lines=[1]}
form = this.fb.nonNullable.group({
  email: ['', {
        validators: [Validators.required, Validators.email]
      }],
  password: ['', [Validators.required, Validators.minLength(8)]]
});
```

或則直接使用 NonNullableFormBuilder 也可達到相同效果：

```ts {linenos=table,hl_lines=[16]}
@Component({
  selector: 'login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.css']
})
export class LoginComponent {

   form = this.fb.group({
      email: ["", {
        validators: [Validators.required, Validators.email]
      }],
      password: ["", [Validators.required, Validators.minLength(8)] ]
    });;

  constructor(private fb: NonNullableFormBuilder) {
  }

}
```


在上述情況下，當你 reset form 時會發現欄位值不會是空值而變成是空字串

```ts {linenos=table,hl_lines=[]}
reset() {

  this.loginForm.reset();

  console.log(this.loginForm.value);

}
```