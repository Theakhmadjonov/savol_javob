
# NestJS Arxitekturasi: Savollar va Javoblar

## 1. Provider yaratish uchun faqat `@Injectable()` dekoratori yetarlimi yoki boshqa shart-sharoitlar ham bormi?

`@Injectable()` dekoratori provider sifatida ishlatilishi uchun **zarur**, ammo **yetarli emas**. Bu dekorator class'ni dependency injection konteyneriga ro'yxatdan o'tkazadi, lekin uni ishlatish uchun u **module`da `providers` arrayiga qo'shilgan bo'lishi kerak**.

**Misol:**
```ts
@Injectable()
export class MyService {
  getHello() {
    return 'Hello';
  }
}

@Module({
  providers: [MyService],
})
export class AppModule {}
```

Aks holda, NestJS bu class’ni provider sifatida tan olmaydi va `Nest can't resolve dependencies` xatosini beradi.

## 2. Agar modul ichida biror provider `exports` arrayiga qo'shilmasa, uni boshqa modul import qilganda qanday muammo yuzaga keladi?

Agar provider `exports` arrayiga qo'shilmasa, uni import qilgan modul undan foydalana olmaydi. U faqat ichki modulda mavjud bo'ladi.

**Muammo yechimlari:**
1. `exports` arrayiga qo‘shish
2. SharedModule yaratib, umumiy providerlarni u yerga joylash

## 3. `@Injectable()` dekoratori qaysi obyektlarga qo'shilishi mumkin va qaysilariga qo'shilmaydi? Express middleware'lariga bu dekorator kerakmi?

`@Injectable()` faqat class'ga qo‘shiladi. Oddiy funksiya yoki object’ga kerak emas. Express middleware agar funksiya bo‘lsa kerak emas. Class sifatida yozilgan bo‘lsa va servis inject qilinayotgan bo‘lsa, kerak bo‘ladi.

## 4. Circular dependency holatida `forwardRef()` dan tashqari qanday yechim bor?

- Interface va abstraktsiyaga asoslangan arxitekturaviy ajratish
- EventEmitter yoki CQRS patternlaridan foydalanish
- `forwardRef()` couplingni oshiradi – undan qochish arxitektura jihatdan foydaliroq

## 5. `@Global()` modulda `exports` bo‘lmasa, foyda bormi?

Yo‘q. Global modul bo‘lishi bilan birga, faqat `exports` qilingan providerlar avtomatik ravishda global ko‘rinadi.

## 6. `provide` va `inject` kalit so‘zlari

`provide` – token yaratadi, `inject` – shu token orqali dependency inject qiladi.

**Misol:**
```ts
{ provide: 'CONFIG', useValue: { port: 3000 } }
@Inject('CONFIG') config
```

## 7. Guard `ExecutionContext` orqali nimalarni oladi? DB chaqirish to‘g‘rimi?

- `req`, `res`, handler, class, method
- DB chaqirish texnik jihatdan mumkin, lekin servis orqali amalga oshirish yaxshiroq (SRP buzilmasin).

## 8. Bir xil providerni bir nechta modullarda `providers` ga qo‘ysak nima bo‘ladi?

- Har bir modul o‘z konteynerida alohida instance yaratiladi
- Singleton qilish uchun shared modul orqali `exports` qilish kerak

## 9. `exports: [UserModule]` qilish mumkinmi?

Yo‘q, noto‘g‘ri. `exports` faqat providerlar va komponentlar uchun. Modulni `imports` orqali ulash kerak.

## 10. Guard `false` qaytarsa HTTP kodi qanday?

- Default: 403 Forbidden
- Boshqa kod uchun: `throw new UnauthorizedException()` yoki `HttpException`

## 11. `useFactory` va dependency

- Inject qilingan dependency’lar avval yaratiladi
- Circular dependency bo‘lsa, `forwardRef()` yoki refactor kerak

## 12. Guard ichida service inject qilib, uni global qilish mumkinmi?

Ha. Nest konteyneri orqali yaratilsagina, dependency inject qilinadi. `APP_GUARD` orqali amalga oshiriladi.

## 13. `providers` va `exports` tokenlari har xil bo‘lsa?

Noto‘g‘ri. Nest tokenlar mos kelishini kutadi. Har doim bir xil tokenni `exports`da ham ishlating.

## 14. Controller va method darajasidagi guardlar – tartib

- Avval controller darajasidagi guardlar, keyin methoddagilar
- Ikkalasi `false` bo‘lsa, birinchisi to‘xtatadi

## 15. DynamicModule providerlari qayerda ishlaydi?

- `exports` orqali boshqa modullarga ochiladi
- DynamicModule boshqa dynamic modulga ham import bo‘lishi mumkin

