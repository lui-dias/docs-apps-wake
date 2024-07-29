Antes de continuar, o app de checkout é um app "low-level", ou seja, ele abstrai algumas coisas como
os cookies, porém coisas como, ao chamar a funçao (action) de cadastrar o usuário, ele não é logado
automaticamente, nem associado ao checkout automaticamente

Por isso vou mostrar o que você precisa chamar em cada ocasião

Repo do template: https://github.com/deco-sites/abacate, ignore o nome Site:
https://abacate.deco.site

## Tópicos

- [Overview do template](#_overview-do-template)
  - [Carrinho](#page-carrinho)
  - [Login](#page-login)
  - [Cadastro](#page-signup)
  - [Frete](#page-frete)
  - [Pagamento](#page-pagamento)
  - [Confirmação](#page-confirmacao)
- [Instalar o app customizado](#instalar)
- [Como usar o app](#como-usar-o-app)
- [Loaders](#loaders)
  - [checkoutCoupon](#_checkoutCoupon)
  - [paymentMethods](#_paymentMethods)
  - [productCustomizations](#_productCustomizations)
  - [userAddresses](#_userAddresses)
- [Actions](#actions)
  - [associateCheckout](#_associateCheckout)
  - [cloneCheckout](#_cloneCheckout)
  - [completeCheckout](#_completeCheckout)
  - [createAddress](#_createAddress)
  - [createCheckout](#_createCheckout)
  - [deleteAddress](#_deleteAddress)
  - [login](#_login)
  - [loginGoogle](#_loginGoogle)
  - [logout](#_logout)
  - [selectAddress](#_selectAddress)
  - [selectInstallment](#_selectInstallment)
  - [selectPayment](#_selectPayment)
  - [selectShipping](#_selectShipping)
  - [shippingSimulation](#_shippingSimulation)
  - [signupCompany](#_signupCompany)
  - [signupPartialCompany](#_signupPartialCompany)
  - [signupPartialPerson](#_signupPartialPerson)
  - [signupPerson](#_signupPerson)
- [FAQ](#faq)
  - [O que é um loader?](#_o-que-e-um-loader)
  - [O que é uma action?](#_o-que-e-uma-action)
  - [O que é um hook?](#_o-que-e-um-hook)
  - [O que é necessário para finalizar uma compra?](#_o-que-e-necessario-para-finalizar-uma-compra)
  - [Por que precisa ter uma página de login?](#_por-que-precisa-ter-uma-pagina-de-login)
  - [Como redirecionar para o /login se o usuário não estiver logado?](#_como-redirecionar-para-o-login-se-o-usuario-nao-estiver-logado)
  - ["An error has occured"](#_an_error_has_occured)

### <a id="_overview-do-template">Overview do template</a>

O template tem bugs ainda, as vezes sendo necessário reiniciar a página para aparecer a informação
correta, mas você consegue finalizar uma compra

<a id="page-carrinho" href="http://localhost:8000/carrinho" style="font-size: 20px; font-weight: 600;">/carrinho</a>

- Mostra os items do carrinho
- Cálculo de frete
- Adicionar cupom

<img src="https://files.catbox.moe/xwll2k.png" height="300">

<a id="page-login" href="http://localhost:8000/login" style="font-size: 20px; font-weight: 600;">/login</a>

- Login
- Redirecionar para o cadastro
- Login/cadastro com Google

<img src="https://files.catbox.moe/bioh89.png" height="300">

<a id="page-signup" href="http://localhost:8000/signup" style="font-size: 20px; font-weight: 600;">/signup</a>

- Cadastro de pessoa física
- Cadastro de pessoa juridica

<img src="https://files.catbox.moe/agrwpc.png" height="300">

<a id="page-frete" href="http://localhost:8000/frete" style="font-size: 20px; font-weight: 600;">/frete</a>

- Selecionar/Adicionar um endereço
- Selecionar frete
- Adicionar um item como presente

<img src="https://files.catbox.moe/2pxvcp.png" height="300">

<a id="page-pagamento" href="http://localhost:8000/pagamento" style="font-size: 20px; font-weight: 600;">/pagamento</a>

- Selecionar forma de pagamento
- Selecionar frete
- Adicionar um item como presente

<img src="https://files.catbox.moe/f1u7s5.png" height="300">

<a id="page-confirmacao" href="http://localhost:8000/confirmacao" style="font-size: 20px; font-weight: 600;">/confirmacao</a>

- Mostra o resumo do pedido
- Mostra status do pagamento

<img src="https://files.catbox.moe/87a5xk.png" height="300">

## App

O "app de checkout" é na verdade o app da wake customizado, enquanto ele não tiver sido mergeado na
deco, é necessário fazer isso

Quando forem colocar a loja pro ar, eu posso fazer o PR pra deco

Ou se quiserem o código, o repo é público https://github.com/lui-dias/apps/tree/feat--checkout-wake,
daí é só trocar o repo e branch do `apps/`

### Instalar

1. Dentro de `deno.json`, troque:

```bash
"apps/": "https://cdn.jsdelivr.net/gh/deco-cx/apps@.../"
```

por

```bash
"apps/": "https://cdn.jsdelivr.net/gh/lui-dias/apps@feat--checkout-wake/"
```

**Lembra de colocar "`/`" no final da URL**

Por enquanto o app de checkout não tá no github da deco porque é mais rápido de subir uma alteração,
se fosse no github da deco eu teria subir um PR e eles precisariam aceitar

2. ???

Se por algum motivo não funcionar seja lá porque, só rodar

```bash
deno task update
deno task cache_clean
```

Como tá usando um apps customizado, o `deno task update`, não vai atualizar a versão do `apps`
automaticamente, apenas a versão do `deco`

De qualquer forma, isso não deve afetar nada, mas se afetar, me chame que eu atualizo a versão dos
`apps`

### Como usar o app

Para o checkout funciona direito, vai no app da wake no admin e ativa o `Headless Checkout`, se isso
não tiver ativado, o app não vai usar o login da api headless e sim da `/api/Login/Get`, e o app de
checkout vai ficar meio estranho

<img src="https://files.catbox.moe/btbvyf.png">

## Loaders

### <a id="_checkoutCoupon">checkoutCoupon</a>

- Retorna o cupom do checkout

```js
invoke.wake.loaders.checkoutCoupon()
```

### <a id="_paymentMethods">paymentMethods</a>

- Retorna as formas de pagamento

```js
invoke.wake.loaders.paymentMethods()
```

### <a id="_productCustomizations">productCustomizations</a>

- Retorna as customizações do produto

```js
invoke.wake.loaders.productCustomizations()
```

### <a id="_userAddresses">userAddresses</a>

Retorna os endereços do usuário

```js
invoke.wake.loaders.userAddresses()
```

## Actions

### <a id="_associateCheckout">associateCheckout</a>

- Associa um usuario a um carrinho, MUITO importante usar isso caso o usuário não tenha um carrinho.
  Os erros da API não estão 100% tratados, tem grande chance de você receber um erro genérico e a
  causa ser o usuário não ter sido associado ao carrinho

```js
invoke.wake.actions.associateCheckout()
```

### <a id="_cloneCheckout">cloneCheckout</a>

- Clona um carrinho

```js
invoke.wake.actions.cloneCheckout()
```

### <a id="_completeCheckout">completeCheckout</a>

- Termina a compra do checkout

```js
// PIX
invoke.wake.actions.completeCheckout({
    comments: 'Esse comentário é totalmente opcional',
})

// Boleto
invoke.wake.actions.completeCheckout({
    comments: 'Esse comentário é totalmente opcional',
    paymentData: {
        cpf: '43188515677',
        telefone: '32982271145',
    },
})

// Cartão
invoke.wake.actions.completeCheckout({
    comments: 'Esse comentário é totalmente opcional',
    paymentData: {
        number: '5364364097413321',
        name: 'sadasdas dasdasdasd',
        month: '10',
        year: '2028',
        cvc: '977',
        expiry: '12/2028',
        cpf: '43188515677',
    },
})
```

Depois de finalizar a compra com sucesso, o carrinho do usuário é "invalidado", daí você precisa
criar um novo

```js
// Deleta o cookie do carrinho
await invoke.wake.actions.deleteCartCookie()
// Gera um novo ("gambiarra")
await invoke.wake.loaders.cart()
// Associa
await invoke.wake.actions.associateCheckout()
```

### <a id="_createAddress">createAddress</a>

- Cria um endereço

```js
invoke.wake.actions.createAddress()
```

### <a id="_createCheckout">createCheckout</a>

- Cria um carrinho

```js
invoke.wake.actions.createCheckout()
```

### <a id="_deleteAddress">deleteAddress</a>

- Deleta um endereço

```js
invoke.wake.actions.deleteAddress({ addressId: '1' })
```

### <a id="_login">login</a>

- Efetua o login

```js
invoke.wake.actions.login({
    'input': 'norberto.donato@geradornv.com.br',
    'pass': 'QhMPtIZLlTj*',
})
```

### <a id="_loginGoogle">loginGoogle</a>

- Efetua o login com o Google

O login com o Google não foi muito testado porque só tenho uma conta Google. Depois que você loga
uma vez e cria a conta no site, você só consegue criar outra conta no site com outra conta Google

```js
invoke.wake.actions.loginGoogle({
    userCredentials: 'eyJFbnRpdHkiOiJQYXltZW50TWV0aG9kIiwiSWQiOjkyMzF9...',
})
```

### <a id="_logout">logout</a>

- Desloga o usuário, apaga o cookie do carrinho, account e do usuário

```js
invoke.wake.actions.logout()
```

### <a id="_selectAddress">selectAddress</a>

- Seleciona um endereço

```js
invoke.wake.actions.selectAddress({ addressId: '1' })
```

### <a id="_selectInstallment">selectInstallment</a>

- Seleciona um parcelamento

```js
invoke.wake.actions.selectInstallment({
    'installmentNumber': 2,
    'selectedPaymentMethodId': '81069108-ef59-400a-844c-833d1e281de6',
})
```

### <a id="_selectPayment">selectPayment</a>

- Seleciona uma forma de pagamento

```js
invoke.wake.actions.selectPayment({
    'paymentMethodId': 'eyJFbnRpdHkiOiJQYXltZW50TWV0aG9kIiwiSWQiOjkyMzF9',
})
```

### <a id="_selectShipping">selectShipping</a>

- Seleciona um frete

```js
invoke.wake.actions.selectShipping({
    'shippingQuoteId': 'eyJFbnRpdHkiOiJQYXltZW50TWV0aG9kIiwiSWQiOjkyMzF9',
})
```

### <a id="_shippingSimulation">shippingSimulation</a>

- Simula o frete

```js
invoke.wake.actions.shippingSimulation({
    'simulateCartItems': true,
    'cep': '57084659',
})
```

### <a id="_signupCompany">signupCompany</a>

- Cria uma conta de empresa

Importante verificar se o usuário já foi associado a um carrinho se não,
[associe](#associatecheckout)

```js
invoke.wake.actions.signupCompany({
    'address': 'Vila Plutão',
    'addressComplement': '213sadasdas',
    'addressNumber': '2131',
    'cep': '66026-374',
    'city': 'Belém',
    'cnpj': '10.034.556/0001-52',
    'email': 'adolfo.richa@geradornv.com.br',
    'corporateName': 'Adolfo França Richa',
    'newsletter': false,
    'neighborhood': 'Jurunas',
    'password': '&klZ(n$Qz2u',
    'passwordConfirmation': '&klZ(n$Qz2u',
    'receiverName': 'Adolfo França Richa',
    'reference': 'asdasdadasdasdasdasd',
    'reseller': false,
    'state': 'PA',
    'primaryPhoneAreaCode': '21',
    'primaryPhoneNumber': '41234-1234',
    'secondaryPhoneAreaCode': '21',
    'secondaryPhoneNumber': '32423-4312',
})
```

### <a id="_signupPartialCompany">signupPartialCompany</a>

- Cria uma conta de empresa parcial, criou a conta com Google, Facebook etc

Importante verificar se o usuário já foi associado a um carrinho se não,
[associe](#associatecheckout)

```js
invoke.wake.actions.signupPartialCompany({
    'cnpj': '10.034.556/0001-52',
    'email': 'adolfo.richa@geradornv.com.br',
    'corporateName': 'Adolfo França Richa',
    'primaryPhoneNumber': '41234-1234',
})
```

### <a id="_signupPartialPerson">signupPartialPerson</a>

- Cria uma conta de usuário parcial, criou a conta com Google, Facebook etc

Importante verificar se o usuário já foi associado a um carrinho se não,
[associe](#associatecheckout)

```js
invoke.wake.actions.signupPartialPerson({
    'cpf': '66026-374',
    'email': 'adolfo.richa@geradornv.com.br',
    'fullName': 'Adolfo França Richa',
    'birthDate': '31/12/1998',
    'primaryPhoneNumber': '41234-1234',
})
```

### <a id="_signupPerson">signupPerson</a>

- Cria um usuário

Importante verificar se o usuário já foi associado a um carrinho se não,
[associe](#associatecheckout)

```js
invoke.wake.actions.signupPerson({
    'address': 'Vila Plutão',
    'addressComplement': '213sadasdas',
    'addressNumber': '2131',
    'birthDate': '31/12/1998',
    'cep': '66026-374',
    'city': 'Belém',
    'cpf': '544.543.632-26',
    'email': 'adolfo.richa@geradornv.com.br',
    'fullName': 'Adolfo França Richa',
    'gender': 'MALE',
    'newsletter': false,
    'neighborhood': 'Jurunas',
    'password': '&klZ(n$Qz2u',
    'passwordConfirmation': '&klZ(n$Qz2u',
    'receiverName': 'Adolfo França Richa',
    'reference': 'asdasdadasdasdasdasd',
    'reseller': false,
    'state': 'PA',
    'primaryPhoneAreaCode': '21',
    'primaryPhoneNumber': '41234-1234',
    'secondaryPhoneAreaCode': '21',
    'secondaryPhoneNumber': '32423-4312',
})
```

## FAQ

### <a id="_o-que-e-um-loader">O que é um loader?</a>

É uma função que retorna alguma coisa

É basicamente o que o GET é pro REST, ou Query pro GraphQL

### <a id="_o-que-e-uma-action">O que é uma action?</a>

É uma função modifica alguma coisa

É basicamente o que o POST, PUT, DELETE ou PATCH é pro REST, ou Mutation pro GraphQL

### <a id="_o-que-e-um-hook">O que é um hook?</a>

[TLDR](#o-que-e-um-hook-tldr)

Supondo que sua loja tem um minicart, wishlist e um ícone pra ver se o usuário tá logado

Toda vez que você chamar alguma action de adicionar um item ao carrinho, wishlist ou seja lá o que,
você precisaria atualizar o minicart por exemplo

```js
cart.value = await invoke.wake.loaders.cart()
```

E se fosse os 3?

```js
cart.value = await invoke.wake.loaders.cart()
user.value = await invoke.wake.loaders.user()
wishlist.value = await invoke.wake.loaders.wishlist()
```

O código fica grande e repetitivo

<span id="o-que-e-um-hook-tldr"></span>

Então os hooks, principalmente `useUser`, `useCart` e `useWishlist`, são estados globais que
retornam signals e actions

A diferenca entre chamar as actions pelos hooks do que chamar pelo `invoke`, é que o hook atualiza o
signal `cart`, toda vez que `addCart`, ou qualquer **action** de `useCart` é chamado

Com hooks

```js
const { cart, addItem } = useCart()

await addItem(...)
console.log(cart.value) // Carrinho com produto adicionado
```

Sem hooks

```js
await invoke.wake.actions.cart.addItem(...)
cart.value = await invoke.wake.loaders.cart()
console.log(cart.value)
```

### <a id="_o-que-e-necessario-para-finalizar-uma-compra">O que é necessário para finalizar uma compra?</a>

1. O usuário deve ter pelo menos 1 produto
2. O usuário deve estar logado e ter um carrinho associado
3. O usuário deve ter um endereço selecionado
4. O usuário deve ter um frete selecionado
5. O usuário deve ter um forma de pagamento selecionada

E se não deu nenhum erro no final da compra, a compra foi feita com sucesso

### <a id="_por-que-precisa-ter-uma-pagina-de-login">Por que precisa ter uma página de login?</a>

A página de login da wake cria um cookie legado que não funciona com a API

Com a página customizada, depois de chamar

```js
invoke.wake.actions.login...()
```

É criado os cookies de login atualizado e o legado, o que permite a API e o my account (MyAccount)
funcionar

### <a id="_como-redirecionar-para-o-login-se-o-usuario-nao-estiver-logado">Como redirecionar para o /login se o usuário não estiver logado?</a>

```js
export async function loader(props: object, req: Request, ctx: AppContext) {
    const isLogged = !!(await ctx.invoke.wake.loaders.user({}, { signal: req.signal }))

    if (!isLogged) {
        return redirect('/login')
    }

    return props
}
```

Basicamente você precisa passar `{ signal: req.signal }` pra evitar que o async rendering faça esse
loader não rodar

Se você não passar isso, esse redirect não funciona

### <a id="_an_error_has_occured">An error has occured</a>

É um erro genérico da API, aconteceu bastante enquanto fazia o app, nem todos os erros da API da
wake são tratados, isso pode ser um erro da sua parte como pode ser da parte deles

Precisa ver a query graphql que foi mandada, e se ainda assim parecer ok, precisa ver com os devs da
wake
