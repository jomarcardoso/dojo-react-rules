# Regras não escritas do React

## Estado

o estado no react é o mesmo de um estado da aplicação, como o react é um biblioteca de renderização, logo os estados possíveis são visuais. Cada estado então é como visualmente aquilo pode estar. Se uma aplicação tem um variável de estado, logo ela vai modificar inteira baseada naquele estado, se tiver 2 já começam a ter variações onde multiplicamos as possibilidades de estado de um com outro, assim vai.

### História do cachorro na banheira

Imagine um cachorro numa banheira, tomando banho e você tem o controle da temperatura dessa água que pode estar em 3 únicos estados congelada, morna e fervendo.

Agora se olharmos isoladamente para o cachorro ele também pode estar em 3 estados congelado, molhado e feliz, e "morrendo de calor".

Isoladamente são variações de estado de cada elemento da nossa história, porém nela os dois se alteram juntos, quando a água está congelada, o cachorro também está, se a água está morna o cachorro está molhado e feliz e se estiver fervendo o cachorro está "morrendo de calor".

Não seria uma verdade na nossa história multiplicar as variações dos estados do cachorro com os da água que resultaria num toltal de 9 estados, o que não seria verdade.

O que você precisa saber sobre os estados do React:

- ele ditará quando deve mudar a renderização;
- não tem a necessidade de ser duplicado, pois pode ser enviado a outros componentes por props;
- ele representa uma "foto" de como está a aplicação.

### 🚫 Estado com valor redundante

Muito importante lembrar, o estado dita a renderização, então não é preciso criar um estado baseado no valor de outro, pois quando aquele mudar, vai passar renderizando tudo abaixo

**errado**
```js
function Somadora() {
  const [número1, setNúmero] = React.useState(0);
  const [número2, setNúmero2] = React.useState(0);
  const [soma, setSoma] = React.useState(0);

  function handleClick() {
    setNúmero(numero1 + 1);
  }

  function handleClick() {
    setNúmero2(numero + 1);
  }

  React.useState(() => {
    setSoma(número1 + número2);
  }, [número]);

  return (
    <div>
      <button onClick={handleClick}>acrescentar número 1</button>
      <button onClick={handleClick2}>acrescentar número 2</button>
      {soma}
    </div>
  );
}
```

**certo**
```js
function Somadora() {
  const [número1, setNúmero] = React.useState(0);
  const [número2, setNúmero2] = React.useState(0);
  const soma = número1 + número2;

  function handleClick() {
    setNúmero(numero1 + 1);
  }

  function handleClick() {
    setNúmero2(numero + 1);
  }

  return (
    <div>
      <button onClick={handleClick}>acrescentar número 1</button>
      <button onClick={handleClick2}>acrescentar número 2</button>
      {soma}
    </div>
  );
}
```

**Estado = DOM**, quando o estado muda a renderização muda, logo, a tela só se atualiza com `setState`. Talvez isso já esteja claro, mas achei bom reforçar.

No caso de aplicações que não usam um máquina de estados como redux, para diminuir o fluxo de props por componentes o estado deve ficar no nível mais baixo possível.

![image](https://user-images.githubusercontent.com/27368585/81748437-3ee2d800-9480-11ea-8eab-ef8d8f62dd55.png)

Na imagem o valor do frete só é necessário para renderizar ali onde aquele componente cuida. Agora digamos que precisamos desse valor no irmão dele, o componente de valor total.

![image](https://user-images.githubusercontent.com/27368585/81748696-a8fb7d00-9480-11ea-880f-bf57ab4ab08a.png)

Agora subimos o estado para todos naquele nível ou abaixo poderem usar.

### Sinais de que isso está sendo feito errado

Quando mais do que um nível tem o mesmo estado.

```js
function CalculaFrete(props) {
  const [valor, setValor] = React.useState(0);

  function handleValor(novoValor) {
    setValor(novoValor);
    props.setValorFrete(novoValor);
  }

  // ...
}
```

### Regra Geral

Não criar estados a não ser que

- vá guardar o retorno de um AJAX.
- vá guardar a interação de um usuário.
- vá guardar um valor antigo que precisa ser revisto
- queira criar um cronômetro ou contador

## Cuidado com loops

Cada vez que setamos um estado o React chama nosso componente de novo e se passar por aquele "setador" de estado ele vai setar e reiniciar o componente sem parar. Algumas coisas que podem ser feitas para isso não acontecer.

### seEffect

Nunca setar estado automaticamente fora do `useEffect`.

Quando o estado é um objeto ou array prefira um `setState` síncrono: `setState(prevValue => ...)`

`Component.propTypes` vem acompanhado do `Component.defaultProps` e ambos devem ser usados.

Lógica no meio do código fica feio.

## Props

As Props tem padrões de nomes e formas de trabalhar com elas.

### Envie o resto das props para o elemento

O intuito é que tudo que está no parâmetro `...props` vá para o primeiro elemento, repare que peguei o id abaixo, mas não tirei ele do props.

Nesse exemplo estou fazendo um controle encadeado de id.

```js
function StepCard({
  label,
  children,
  disabled,
  ...props
}) {
  const { id } = props;

  return (
    <div className="card" {...props}>
      <Accordion
        id={`${id}-accordion`}
        label={label}
      >
        <div className="card__body">
          {children}
        </div>
      </Accordion>
    </div>
  )
}
```

### Nomenclatura padrão de props

As especificações da W3C ditam o nome de atributos que os navegadores devem seguir, porém a equipe do React sobrescreve alguns deles em código, mas que no resultado compilado é igual, por exemplo o atributo `onclick` que em React é `onClick`.

Então por ordem de prioridade:

1. nomenclatura padrão do react
2. nomenclatura padrão da web
3. personalizado pelo desenvolvedor

Trabalhar com padrões facilita o entendimento de quem irá dar manutenção.

**errado**
```js
function CustomForm() {
  return (
    <CustomInput customClass="input-custom" />
  );
}
```

**errado**
```js
function CustomForm() {
  return (
    <CustomInput className="input-custom" />
  );
}
```

### Nomenclatura de props de eventos

Para o elemento mandamos com o prefixo `on`, dentro do componente manipulamos com o prefixo `handle`.

```js
function CustomButton({ onClick, children, ...props }) {
  handleClick(event) {
    onClick(event);
  }
  
  return <button onClick={handleClick} {...props}>{button}</button>;
}
```

### ClassName & Classes

O classes é uma regra não escrita onde enviamos um objeto com as classes de um componente complexo.

**exemplo do Material UI**
```js
<Button
  classes={{
    root: classes.root, // class name, e.g. `classes-nesting-root-x`
    label: classes.label, // class name, e.g. `classes-nesting-label-x`
  }}
>
  classes nesting
</Button>
```

## Fazendo gambiarras do bem

### Editar um elemento filho

Sempre clonar ele para não acontecer erros de quebra da imutabilidade.

```js
export function Radio({ input, className, ...props }) {
  const clonedInput = React.cloneElement(input);
  const { id } = props;
  const classes = `ty-radio ${className}`;

  clonedInput.props.className = 'ty-radio__input';
  clonedInput.props = {
    ...props,
    ...clonedInput.props,
  };

  return (
    <label className={classes} htmlFor={id}>
      {clonedInput}
      <div className="ty-radio__checkmark" />
    </label>
  );
}
```

## UseEffect

### Dar nome a eles

```js
function verifyCartDataAndUpdateDeliveries() {
  const { cep } = values;
  const { hasDeliveryOptions: notFetched } = cartData;

  if (!cep || notFetched) return;

  fetchByCep(cep);
}

function verifyAndGetByPersistentCEP() {
  const persistentCep = DeliveryCalculateService.getCepFromCookies();

  if (persistentCep) {
    setValueByName('cep', persistentCep);
  }
}

useEffect(() => {
  verifyCartDataAndUpdateDeliveries();
}, [cartData]);

useEffect(() => {
  verifyAndGetByPersistentCEP();
}, []);
```

## Ordem de métodos

1. estados e variáveis
2. outros
3. manipuladores de eventos
4. métodos render
5. useEffects
