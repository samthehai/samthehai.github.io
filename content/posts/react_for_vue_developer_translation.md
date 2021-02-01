---
title: "React.js dành cho dân lập trình Vue.js"
date: "2020-12-20"
---

# 1. Giới thiệu

Đang học React.js để áp dụng cho các dự án đang làm thì bí stuck một số chỗ.
Nhân tiện kiếm được trên mạng một tài liệu mình thấy hay giới thiệu về React cho những ai đã có nền tảng Vue.js
Mình lược dịch lại tài liệu này sang Tiếng Việt như một cách để active learning luôn.

## Table of contents

- Templates
- Props
- Data
- Computed properties
- Methods
- Events
- Lifecycle methods
- Watchers
- Slots & scoped slots
- Provide / inject
- Custom directives
- Transitions

# 2. Templates

Tương đương bên React: JSX

Vue sử dụng HTML strings với một vài custom directives. Thường thì .vue filé sé được sử dụng để tách biệt templates và script và có thể có styles nữa.

```html
<!-- Greeter.vue -->

<template>
  <p>Hello, {{ name }}!</p>
</template>

<script>
  export default {
    props: ["name"],
  };
</script>
```

React sử dụng một script mở rộng của ECMAScript gọi là JSX.

```jsx
export default function Greeter({ name }) {
  return <p>Hello, {name}!</p>;
}
```

## 2.1. Conditional rendering

Tương đương bên React: Sử dụng logic && operator, tenary statements hoặc là early return

Vue sử dụng v-if, v-else và v-else-if để render có điều kiện một phần của templates

```html
<!-- Awesome.vue -->

<template>
  <article>
    <h1 v-if="awesome">Vue is awesome!</h1>
  </article>
</template>

<script>
  export default {
    props: ["awesome"],
  };
</script>
```

React thì không hỗ trợ directive nên là bạn phải sử dụng ngôn ngữ lập trình để trả về có điều kiện một phần của templates.

```jsx
export default function Awesome({ awesome }) {
  return <article>{awesome && <h1>Vue is awesome!</h1>}</article>;
}
```

Trong trường họp cần else statement thì bạn phải sử dụng tenary statements

```jsx
export default function Awesome({ awesome }) {
  return (
    <article>
      {awesome ? <h1>React is awesome!</h1> : <h1>Oh no 😢</h1>};
    </article>
  );
}
```

Bạn cũng có thể tách biêt hai xử lý bằng cách sử dụng early return

```jsx
export default function Awesome({ awesome }) {
  if (!awesome) {
    return (
      <article>
        <h1>Oh no 😢</h1>
      </article>
    );
  }

  return (
    <article>
      <h1>React is awesome!</h1>
    </article>
  );
}
```

## 2.2. List rendering

Tương đương bên React: Array.map

Vue sử dụng v-for directive để lặp qua objects và arrays

```html
<!-- Recipe.vue -->

<template>
  <ul>
    <li v-for="(ingredient, index) in ingredients" :key="index">
      {{ ingredient }}
    </li>
  </ul>
</template>

<script>
  export default {
    props: ["ingredients"],
  };
</script>
```

Với React bạn có thể map array để tạo danh sách elements sử dụng hàm build in Array.map của javascript

```jsx
export default function Recipe({ ingredients }) {
  return (
    <ul>
      {ingredients.map((ingredient, index) => (
        <li key={index}>{ingredient}</li>
      ))}
    </ul>
  );
}
```

Duyệt qua object thì khó hơn. Vue cho phép sử dụng v-for directives để duyệt qua keys và values.

```html
<!-- KeyValueList.vue -->

<template>
  <ul>
    <li v-for="(value, key) in object" :key="key">{{ key }}: {{ value }}</li>
  </ul>
</template>

<script>
  export default {
    props: ["object"], // E.g. { a: 'Foo', b: 'Bar' }
  };
</script>
```

Cá nhân tôi thì thích sử dụng hàm builtin Object.entries với React để duyệt qua object.

```jsx
export default function KeyValueList({ object }) {
  return (
    <ul>
      {Object.entries(object).map(([key, value]) => (
        <li key={key}>{value}</li>
      ))}
    </ul>
  );
}
```

## 2.3. Class và style bindings

Tương đương bên React: Truyền props theo cách manual

Vue tự động binds class và style props xuống cho HTML element ngoài cùng của một component.

```html
<!-- Post.vue -->

<template>
  <article>
    <h1>{{ title }}</h1>
  </article>
</template>

<script>
  export default {
    props: ["title"],
  };
</script>

<!--
<post
  :title="About CSS"
  class="margin-bottom"
  style="color: red"
/>
-->
```

Với React, bạn cần truyền className và style props môt cách manually. Chú ý rằng style phải là một object với React, strings thì không được support.

```jsx
export default function Post({ title, className, style }) {
  return (
    <article className={className} style={style}>
      {title}
    </article>
  );
}

{
  /* <Post
  title="About CSS"
  className="margin-bottom"
  style={{ color: 'red' }}
/> */
}
```

Nếu bạn muốn truyền xuống tất cả các props còn lại, có thể sử dụng spread operator.

```jsx
export default function Post({ title, ...props }) {
  return <article {...props}>{title}</article>;
}
```

Nếu bạn muốn sử dụng API class giống như bên Vue, có thể tham khảo thử viện [classnames](https://github.com/JedWatson/classnames) của Jed Watson.

# 3. Props

Tương đương vên React: Props

Props bên React hoạt dộng gần như tương tự bên Vue. Chỉ có một khác biệt nhỏ: React không [thừa kế](https://vuejs.org/v2/api/#inheritAttrs) những atributes không được định nghĩa

```html
<!-- Post.vue -->

<template>
  <h1>{{ title }}</h1>
</template>

<script>
  export default {
    props: ["title"],
  };
</script>
```

```jsx
export default function
```

```jsx
export default function Post({ title }) {
  return <h3> {{ title }}</h3>;
}
```

Có thể sử dụng dynamic bind bằng cách sử dụng prefix :, đây chính là alias của directive v-bind. React thì lại sử dụng dấu ngoặc nhọn.

```html
<!-- Post.vue -->

<template>
  <post-title :title="title" />
</template>

<script>
  export default {
    props: ["title"],
  };
</script>
```

```jsx
export default function Post({ title }) {
  return <PostTitle title={title} />;
}
```

## 4. Data

Tương đương bên React: useState hook

Trong Vue data option được sử dụng dể chứa trạng thái local của component.

```html
<!-- ButtonCounter.vue -->

<template>
  <button @click="count++">You clicked me {{ count }} times.</button>
</template>

<script>
  export default {
    data() {
      return {
        count: 0,
      };
    },
  };
</script>
```

React thì sử dụng useState hook, sẽ trả về một array có hai phần tử chứa trạng thái hiện tại và một hàm setter.

```jsx
import { useState } from "react";

export default function ButtonCounter() {
  const [count, setCounter] = useState(0);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

Bạn có thể chia mỗi state ra bằng cách gọi useState nhiều lần hoặc gộp lại gọi một lần.

```jsx
import { useState } from "react";

export default function ProfileForm() {
  const [name, setName] = useState("Sebastian");
  const [email, setEmail] = useState("sebastian@spatie.be");

  // ...
}
```

```jsx
import { useState } from "react";

export default function ProfileForm() {
  const [values, setValues] = useState({
    name: "Sebastian",
    email: "sebastian@spatie.be",
  });

  // ...
}
```

## 4.1. v-model

v-model là một directive rất hữu ích trong Vue, nó kết hợp việc truyền giá trị xuống thông qua prop với việc lắng nghe input event. Điều này có thể khiến gây nhầm lẫn rằng Vue thực hiện biding hai chiều, nhưng thật ra cơ chế ẩn dưới vẫn là “truyền props xuống, nhận events trả lên”.

```html
<!-- Profile.vue -->

<template>
  <input type="text" v-model="name" />
</template>

<script>
  export default {
    data() {
      return {
        name: "Sebastian",
      };
    },
  };
</script>
```

Có thể triển khai đầy đủ v-model thành như dưới này:

```html
<template>
  <input type="text" :value="name" @input="name = $event.tagert.value" />
</template>
```

Tuy nhiên bên React không có cách biểu diễn tương tự, bạn luôn phải viết tường mình.

```jsx
import { useState } from "react";

export default function Profile() {
  const [name, setName] = useState("Sebastian");

  return (
    <input
      type="text"
      value={name}
      onChange={(event) => setName(event.target.name)}
    />
  );
}
```

## 4.2. Computed properties

Tương đương bên React: biến, wrap lại bằng useMemo

Có hai lý do cho việc tồn tại của computed properties trong Vue: dể tránh việc trộn lẫn giữa logic và markup trong template, để cache những phần đòi hỏi tính toán phức tạp trong component state.

Nếu mà không có computed:

```html
<!-- ReversedMessage.vue -->

<template>
  <p>{{ message.split('').reverse().join('') }}</p>
</template>

<script>
  export default {
    props: ["message"],
  };
</script>
```

```jsx
export default function ReversedMessage({ message }) {
  return <p>{message.split("").reverse().join("")}</p>;
}
```

Trong React bạn có thể tách phần logic khỏi template bằng cách gán giá trị kết quả vào một biến.

```html
<!-- ReversedMessage.vue -->

<template>
  <p>{{ reversedMessage }}</p>
</template>

<script>
  export default {
    props: ["message"],

    computed: {
      reversedMessage() {
        return this.message.split("").reverse().join("");
      },
    },
  };
</script>
```

```jsx
export default function ReversedMessage({ message }) {
  const reversedMessage = message.split("").reverse().join("");

  return <p>{reversedMessage}</p>;
}
```

Nếu chú trọng đến performance, việc tính toán có thể được wrap lại bằng cách sử dụng useMemo hook, useMemo yêu cầu phải có một hàm callback trả về kết quả tính toán và một mảng của dependencies.

Trong ví dụ sau đây `reversedMessage` chỉ phải bị tính toán lại nếu như biến `message` nó phụ thuộc bị thay đổi

```jsx
import { useMemo } from "react";

export default function ReversedMessage({ message }) {
  const reversedMessage = useMemo(() => {
    return message.split("").reverse().join("");
  }, [message]);

  return <p>{reversedMessage}</p>;
}
```

# 5. Methods

Tương đương bên React: Functions

Vue có một options tên là methods để khai báo các hàm được sủ dụng bên trong component

```html
<!-- ImportantButton.vue -->

<template>
  <button onClick="doSomething">Do something!</button>
</template>

<script>
  export default {
    methods: {
      doSomething() {
        // ...
      },
    },
  };
</script>
```

Trong React bạn có thể khai báo một functions bình thường ở bên trong component.

```jsx
export default function ImportantButton() {
  function doSomething() {
    // ...
  }

  return <button onClick={doSomething}>Do something!</button>;
}
```

# 6. Events

Tương đương bên React: Callback props

Events là những callback cần thiết được gọi khi có những sự kiện xảy ra phía component con. Events là một thành phần quan trọng trong Vue, bạn có thể lắng nghe nó sử dụng @, là thể ngắn gọn của v-on.

```html
<!-- PostForm.vue -->

<template>
  <form>
    <button type="button" @click="$emit('save')">Save</button>
    <button type="button" @click="$emit('publish')">Publish</button>
  </form>
</template>
```

Events không có bất kỳ ý nghĩa đặc biệt nào như vậy bên React, nó đơn giản chỉ là những callback prop được gọi bởi những component con.

```jsx
export default function PostForm({ onSave, onPublish }) {
  return (
    <form>
      <button type="button" onClick={onSave}>
        Save
      </button>
      <button type="button" onClick={onPublish}>
        Publish
      </button>
    </form>
  );
}
```

## 6.1. Event modifier

Tương đương bên React: Higher order function

Vue có một vài modifier như `prevent`, `stop` để thay đổi cách mà event được handle mà không cần phải chỉnh sửa lại handler.

```html
<!-- AjaxForm.vue -->

<template>
  <form @submit.prevent="submitWithAjax">
    <!-- ... -->
  </form>
</template>

<script>
  export default {
    methods: {
      submitWithAjax() {
        // ...
      },
    },
  };
</script>
```

Không có những modiffier như vậy trong React. Prevent default và stopping hầu như sẽ được xử lý bên trong hàm callback.

```jsx
export default function AjaxForm() {
  function submitWithAjax(event) {
    event.preventDefault();
    // ...
  }

  return <form onSubmit={submitWithAjax}>{/* ... */}</form>;
}
```

Nếu bạn thực muốn có cái gì đó giống như là modifier bạn có thể sử dụng higher order function.

```jsx
function prevent(callback) {
  return (event) => {
    event.preventDefault();
    callback(event);
  };
}

export default function AjaxForm() {
  function submitWithAjax(event) {
    // ...
  }

  return <form onSubmit={prevent(submitWithAjax)}>{/* ... */}</form>;
}
```

# 7. Lifecycle methods

Tương đương bên React: `useEffect` hook

Đối với class component, React có những [API tương tự](https://reactjs.org/docs/react-component.html#the-component-lifecycle) Vue nếu nói về component lifecycle. Với hook, hầu như tất cả các vấn đề tương tự có thể được giải quyết bằng cách sử dụng `useEffect`. Nói chung effects và lifecycle methods có cơ chế khác nhau nên là khó để so sánh, phần này chỉ giới hạn việc mô tả vài ví dụ. Effect xứng đáng có một phần riêng để bàn.

Thông thường vai trò của lifeccycle methods là để setup và teardown những thư viện bên thứ ba.

```html
<template>
  <input type="text" ref="input" />
</template>

<script>
  import DateTimePicker from "awesome-date-time-picker";

  export default {
    mounted() {
      this.dateTimePickerInstance = new DateTimePicker(this.$refs.input);
    },
    beforeDestroy() {
      this.dateTimePickerInstance.destroy();
    },
  };
</script>
```

Sử dụng `useEffect` bạn có thể khai báo một `side effect` mà cần phải chạy sau khi render. Khi bạn trả về một hàm callback từ `useEffect`, nó sẽ được gọi khi effect được cleanup, có nghĩa là khi component bị destroy.

```jsx
import { useEffect, useRef } from "react";
import DateTimePicker from "awesome-date-time-picker";

export default function Component() {
  const dateTimePickerRef = useRef();

  useEffect(() => {
    const dateTimePickerInstance = new DateTimePicker(
      dateTimePickerRef.current
    );
    return () => {
      dateTimePickerInstance.destroy();
    };
  }, []);

  return <input type="text" ref={dateTimePickerRef} />;
}
```

Việc này tương tự đăng ký một `beforeDestroy` listener trong mounted ở một Vue component.

```html
<script>
  export default {
    mounted() {
      const dateTimePicker = new DateTimePicker(this.$refs.input);

      this.$once("hook:beforeDestroy", () => {
        dateTimePicker.destroy();
      });
    },
  };
</script>
```

Tương tự `useMemo`, `useEffect` nhận vào một array các biến phụ thuộc, như là parameter thứ hai.

Nếu không khai báo bất kỳ biến phụ thuộc nào, effect sẽ chạy sau mỗi lần render, và sẽ được clean up trước khi mỗi lần render kế tiếp. Chức năng này tương tự như một sự kết hợp giữa `mounted`, `updated`, `beforeUpdate` and `beforeDestroy`.

```jsx
useEffect(() => {
  // Happens after every render

  return () => {
    // Optional; clean up before next render
  };
});
```

Nếu bạn chỉ định effect không có biến phụ thuộc nào, effect sẽ chỉ chạy lần đầu tiên khi component render lần đầu tiên, bởi vì nó không có lý do gì để cập nhật. Chức năng này tương tự như sự kết hợp giữa `mounted`, và `beforeDestroyed`.

```jsx
useEffect(() => {
  // Happens on mount

  return () => {
    // Optional; clean up before unmount
  };
}, []);
```

Nếu bạn chỉ định biến phụ thuộc effect sẽ chỉ chạy khi các biến phụ thuộc có thay đổi. Chúng ta sẽ quay trở lại đề tài này trong phần nói về `watcher`.

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  // Happens when `count` changes

  return () => {
    // Optional; clean up when `count` changed
  };
}, [count]);
```

Cố gắng để translate một lifecycle thành effect không phải là ý tưởng hay. Sẽ tốt hơn nếu bạn xem mọi thứ như một tập hợp của những side effect được khai báo. Và các effect được gọi là những phần hiện thực chi tiết.

Giống như Ryan Florence đã tổng hợp dưới này:

```
The question is not “when does this effect run” the question is “with which state does this effect synchronize with”

useEffect(fn) // all state
useEffect(fn, []) // no state
useEffect(fn, [these, states])
```

# 8. Watchers

Tương đương bên React: `useEffect` hook

Khái niệm về Watcher tương tự như là lifecycle hook: "Khi X xảy ra, thực hiện Y". Watcher không tồn tại trong React, nhưng bạn có thể đạt được hiệu ứng tương tự với `useEffect`.

```html
<!-- AjaxToggle.vue -->

<template>
  <input type="checkbox" v-model="checked" />
</template>

<script>
  export default {
    data() {
      return {
        checked: false,
      };
    },

    watch: {
      checked(checked) {
        syncWithServer(checked);
      },
    },

    methods: {
      syncWithServer(checked) {
        // ...
      },
    },
  };
</script>
```

```jsx
import { useEffect, useState } from "react";

export default function AjaxToggle() {
  const [checked, setChecked] = useState(false);

  function syncWithServer(checked) {
    // ...
  }

  useEffect(() => {
    syncWithServer(checked);
  }, [checked]);

  return (
    <input
      type="checkbox"
      checked={checked}
      onChange={() => setChecked(!checked)}
    />
  );
}
```

Chú ý `useEffect` cũng sẽ chạy ở lần đầu tiên. Điều này tương tự như khi sử dụng `immediate` parameter trong Vue watcher.

Nếu bạn không muốn effect chạy ở lần đầu tiên bạn phải tạo một `ref` để lưu lại về việc render lần đầu tiên đã được chạy hay chưa.

```jsx
import { useEffect, useRef, useState } from "react";

export default function AjaxToggle() {
  const [checked, setChecked] = useState(false);

  const firstRender = useRef(true);

  function syncWithServer(checked) {
    // ...
  }

  useEffect(() => {
    if (firstRender.current) {
      firstRender.current = false;
      return;
    }
    syncWithServer(checked);
  }, [checked]);

  return (
    <input
      type="checkbox"
      checked={checked}
      onChange={() => setChecked(!checked)}
    />
  );
}
```

# 9. Slots & scoped slots

Tương đương bên React: JSX props or render props

Nếu bạn render một template ở giữa tag open và close của component, react sẽ truyền nó như là children prop.

Với Vue bạn có thể khai báo một `<slot />` tag nơi mà content thuộc về. Với React thì bạn sẽ render child prop.

```html
<!-- RedParagraph.vue -->

<template>
  <p style="color: red">
    <slot />
  </p>
</template>
```

```jsx
export default function RedParagraph({ children }) {
  return <p style={{ color: "red" }}>{children}</p>;
}
```

Bởi vì `slots` chỉ có vai trò như là prop trong React, chúng ta không cần phải khai báo bất cứ gì trong template, ta chỉ cần nhận vào props sử dụng JSX và render chúng ở bất cứ nơi nào và thời điểm mình muốn.

```html
<!-- Layout.vue -->

<template>
  <div class="flex">
    <section class="w-1/3">
      <slot name="sidebar" />
    </section>
    <main class="flex-1">
      <slot />
    </main>
  </div>
</template>

<!-- In use: -->

<layout>
  <template #sidebar>
    <nav>...</nav>
  </template>
  <template #default>
    <post>...</post>
  </template>
</layout>
```

```jsx
export default function RedParagraph({ sidebar, children }) {
  return (
    <div className="flex">
      <section className="w-1/3">{sidebar}</section>
      <main className="flex-1">{children}</main>
    </div>
  );
}

// In use:

return (
  <Layout sidebar={<nav>...</nav>}>
    <Post>...</Post>
  </Layout>
);
```

Vue có scoped slots để truyền data xuống slot sẽ được render.
Những phần chính của scopped slot sẽ được render.

Thông thường slot sẽ được render trước khi chúng được truyền xuống parent component. Component cha sẽ quyết định sẽ làm gì với những fragment được render.

Scoped slot không thể được render trước component cha bởi vì nó cần dữ liệu truyền vào của component cha, nói cách khác scoped slot là lazily evaluated slot.

Lazily trong Javascript thì rõ ràng: chỉ cần wrap nó lại trong một function và gọi nó khi cần thiết. Nếu bạn cần một scoped slot trong React chỉ cần truyền vào một hàm mà trả về một template khi được gọi.

Đối với một scoped slot, chúng ta có thể một lần nữa sử dụng children hoặc bất cứ prop nào khác như là named scoped slot, tuy nhiên chúng ta sẽ truyền vào một hàm thay vì trả về một template.

```html
<!-- CurrentUser.vue -->

<template>
  <span>
    <slot :user="user" />
  </span>
</template>

<script>
  export default {
    inject: ["user"],
  };
</script>

<!-- In use: -->

<template>
  <current-user>
    <template #default="{ user }"> {{ user.firstName }} </template>
  </current-user>
</template>
```

```jsx
import { useContext } from "react";
import UserContext from "./UserContext";

export default function CurrentUser({ children }) {
  const { user } = useContext(UserContext);

  return <span>{children(user)}</span>;
}

// In use:

return <CurrentUser>{(user) => user.firstName}</CurrentUser>;
```

# 10. Provide / inject

Tương đương bên React: `createContext` và `useContext` hook

`Provide / inject` cho phép một component share state cho nhánh con của của. Tương tự bên React có context.

```html
<!-- MyProvider.vue -->

<template>
  <div><slot /></div>
</template>

<script>
  export default {
    provide: {
      foo: "bar",
    },
  };
</script>

<!-- Must be rendered inside a MyProvider instance: -->

<template>
  <p>{{ foo }}</p>
</template>

<script>
  export default {
    inject: ["foo"],
  };
</script>
```

```jsx
import { createContext, useContext } from "react";

const fooContext = createContext("foo");

function MyProvider({ children }) {
  return <FooContext.Provider value="foo">{children}</FooContext.Provider>;
}

// Must be rendered inside a MyProvider instance:

function MyConsumer() {
  const foo = useContext(FooContext);

  return <p>{foo}</p>;
}
```

# 11. Custom directives

Tương đương bên React: Components

React không có khái niệm directive, tuy nhiên mọi vấn đề giải quyết được bằng directive đều có thể giải quyết được bằng component.

```html
<div v-tooltip="Hello!">
  <p>...</p>
</div>
```

```jsx
return (
  <Tooltip text="Hello">
    <div>
      <p>...</p>
    </div>
  </Tooltip>
);
```

# 12. Transition

Tương đương bên React: Thư viện bên thứ ba

React không có hàm builtin hỗ trợ transition, nếu bạn tìm kiếm những gì tương tự như bên Vue bạn có thể sử dụng thư viện khác như là:
- https://github.com/reactjs/react-transition-group
- https://www.framer.com/api/motion/



# Nguồn tham khảo

https://sebastiandedeyne.com/react-for-vue-developers/
