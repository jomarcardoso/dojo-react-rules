# Regras não escritas do React

## O nível do estado

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

## Cuidado com loops

Cada vez que setamos um estado o React chama nosso componente de novo e se passar por aquele "setador" de estado ele vai setar e reiniciar o componente sem parar. Algumas coisas que podem ser feitas para isso não acontecer.

### seEffect

Nunca setar estado automaticamente fora do `useEffect`.

Quando o estado é um objeto ou array prefira um `setState` síncrono: `setState(prevValue => ...)`

`Component.propTypes` vem acompanhado do `Component.defaultProps` e ambos devem ser usados.

Lógica no meio do código fica feio.
