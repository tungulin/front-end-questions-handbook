## Распространенные вопросы React:

### 1. Зачем React нужны ключи?

 > Представьте, что файлы на вашем рабочем столе не имеют имен. Вместо этого вы будете обращаться к ним по порядку — первый файл, второй файл и так далее. К этому можно было бы привыкнуть, но как только вы удалите файл, это приведет к путанице. Второй файл станет первым файлом, третий файл станет вторым файлом и так далее.

Если быть точнее, для того, чтобы React мог определить, какой компонент из списка ей необходимо перерисовать. Для этого требуются ключи. **Без ключей React не будет перерисовывать только измененный компонент в списке, а просто перерисует весь список**.
 
Представим, что список состоит из сложных компонентов с большой логикой внутри. В даннои случаи ключи помогут React выбрать какой компонент надо перерисовать, не затрагивая другие сложные компоненты в списке. 

**Вообще список являются частным случаем. Есть также другие случаи когда стоит также указывать ключи**

<!-- todo: Add example -->

Для более четкого объяснения того, как и когда React решает перерисовать компонент, я рекомендую прочитать [react-under-the-hood](react-under-the-hood.md)


### 2.Что такое HOC в React?

  > HOC (High order component) — это функция, которая принимает один компонент в качестве входных данных и возвращает новый с более расширенными функциями в качестве выходных данных. Другими словами, hoc — это функция, которая ожидает в качестве параметров компонент (назовем его входным), который обернут в другой компонент, объявленный в теле функции, который действует как значение, возвращаемое из функции (назовем его выходным).

**Пример Hign-order-component(HOC):**

```jsx
export const WithAuthorize = (isAuthorized,
  {ComponentForAuthorized, ComponentForUnauthorized}) => {
      
    const WrappedComponentWithAuth = (props) => {
      if(isAuthorized) {
        return <ComponentForAuthorized {...props} />
      }
      
      return <ComponentForUnauthorized {...props} />
    }

    return WrappedComponentWithAuth
}
```

**Пример использования Hign-order-component(HOC):**

```jsx
const isAuthorize = true // Checking the user is authorized
const ProfileWithAuthorize = WithAuthorize(isAuthorize, ProfileFullInfo, ProfilePreview)
```

Что тут происходит? Все просто. Создается стрелочная функция (очень похожая на функциональный компонент) **WithAuthorize**. Который принимает два параметра: 
1. **isAuthorized** — это флаг авторизован пользователя или нет.

2. **{ComponentForAuthorized, ComponentForUnauthorized}** — объект, состоящий из двух компонентов.
    1. ComponentForAuthorized — это компонент, который будет отображаться, если пользователь авторизован. 
    2. ComponentForUnauthorized — это компонент, который будет отображаться, если пользователь не авторизован.

В зависимости от того, авторизован пользователь или нет, **WrappedComponentWithAuth** вернет тот или иной объект. **WithAuthorize** в своем же случае возвращает WrappedComponentWithAuth.

**Основное преимущество HOC** заключается в том, что они позволяют нам расширять функциональность нескольких компонентов, не повторяя один и тот же код в каждом из них. Это способствует повторному использованию кода и улучшает удобство обслуживания React приложения. 

В приведенном выше примере HOC является удобной функцией, для определения какой компонент показывать в зависимости от того авторизирован пользователь или нет. Если авторизирован возвращается - ProfileFullInfo, если нет - ProfilePreview.

Еще одно не очевидное, но **важное преимущество** - не вызываются лишние хуки компонентов ProfileFullInfo и ProfilePreview.

Вы можете спросить меня, почему вы не можете просто условно отобразить эти компоненты в JSX:

```jsx
const Component = () => {
  const [isAuthorized, setIsAuthorized] = useState(false)

  useEffect(()=>{
    fetchData() // Change isAuthorized = true
  },[])

  return isAuthorized ? ProfileFullInfo : ProfilePreview
}
```

Первое состояние стейта **isAuthorized = false**, поскольку мы еще не получили данные и не авторизивали пользователя. По этой причине компонент ProfilePreview будет отображаться первым. Этот компонент может содержать много логики, построенной на хуках React. Надо учитывать, что мы не можем условно выполнять хуки. Они должны быть выполнены. После **isAuthorized = true** будет отображен компонент ProfileFullInfo. Который также выполнит все свои хуки React.

Так зачем нам нужно выполнять хуки в компоненте ProfilePreview? Правильно не зачем :) Вот HOC и решает эту проблему. 

<!--todo: По сути мы изолируем все  хуки React компонетна ProfilePreview -->