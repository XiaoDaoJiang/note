# Getting Started: Project Structure | Next.js
来源：[Getting Started: Project Structure | Next.js](https://nextjs.org/docs/app/getting-started/project-structure#dynamic-routes)

## 摘录内容

### [Dynamic routes](#dynamic-routes)

\| [`[folder]`](/docs/app/api-reference/file-conventions/dynamic-routes#convention) | Dynamic route segment |
\| [`[...folder]`](/docs/app/api-reference/file-conventions/dynamic-routes#catch-all-segments) | Catch-all route segment |
\| [`[[...folder]]`](/docs/app/api-reference/file-conventions/dynamic-routes#optional-catch-all-segments) | Optional catch-all route segment |

## 想法

1.  `[folder]` 动态匹配一层路径，如：/api/user/[folder]/post => /api/user/123/post
2.  `[...folder]`动态匹配多个路由段，如：/api/user/[...folder]/star => /api/user/123/star,/api/user/123/456/star 等
3.  `[[...folder]]`：与 `[...folder]`类似，但是可以路由段是可选的，如：/api/user/\[[...folder]] => /api/user,/api/user/123,/api/user/123/456 等

## 详细

在 **Next.js 15** 中，动态路由的目录命名规则仍然基于文件系统结构，并且适用于 **App Router** 和 **Pages Router**。以下是详细的动态路由目录命名规则说明。

* * *

## ✅ Next.js 15 动态路由命名规则（App Router & Pages Router）

| 路由类型                         | 文件路径                               | URL 示例                                      | 匹配的路径                                |
| ---------------------------- | ---------------------------------- | ------------------------------------------- | ------------------------------------ |
| 静态路由                         | `app/about/page.tsx`               | `/about`                                    | 只匹配 `/about`                         |
| 动态路由                         | `app/posts/[id]/page.tsx`          | `/posts/123`                                | 匹配 `/posts/123`, `/posts/abc` 等      |
| 多段动态路由                       | `app/[username]/settings/page.tsx` | `/john/settings`                            | 匹配任意用户名下的 settings 页面                |
| 捕获所有路由（Catch-all）            | `app/blog/[...slug]/page.tsx`      | `/blog/a/b/c`                               | 匹配 `/blog`, `/blog/a`, `/blog/a/b/c` |
| 可选捕获所有路由（Optional Catch-all） | `app/search/[[...query]]/page.tsx` | `/search`, `/search/foo`, `/search/foo/bar` | 支持空参数或多个参数                           |

> 📌 所有动态路由参数都通过 `params` 对象传入页面组件中。

* * *

### 🔧 App Router 示例

```tsx
// app/posts/[id]/page.tsx
export default function PostPage({ params }: { params: { id: string } }) {
  return <div>Post ID: {params.id}</div>;
}
```

你还可以使用 `useParams` Hook：

```tsx
'use client';

import { useParams } from 'next/navigation';

export default function PostPage() {
  const params = useParams();
  return <div>Post ID: {params.id}</div>;
}
```

* * *

### 🔧 Pages Router 示例（兼容模式）

```tsx
// pages/posts/[id].tsx
import { useRouter } from 'next/router';

export default function PostPage() {
  const router = useRouter();
  const { id } = router.query;

  return <div>Post ID: {id}</div>;
}
```

* * *

## 📁 命名注意事项

-   **动态路由文件夹必须用方括号包裹**：如 `[id]`、`[...slug]`。
-   **不能嵌套动态路由文件夹名称相同**，例如：
    -   ❌ 错误：`app/[id]/[id]/page.tsx`
    -   ✅ 正确：`app/[id]/details/page.tsx`
-   **捕获所有路由语法必须严格使用三个点**：`[...slug]` 或 `[[...query]]`。
-   在 TypeScript 项目中，建议为 `params` 添加类型注解以获得更好的类型推导。

* * *

## 🧠 常见问题解答

### 1. 如何获取多个动态参数？

```ts
// app/[category]/products/[id]/page.tsx
export default function ProductPage({ params }: { params: { category: string; id: string } }) {
  return (
    <div>
      Category: {params.category}, ID: {params.id}
    </div>
  );
}
```

### 2. 如何处理可选参数？

使用 `[[...slug]]` 来创建可选捕获所有路由：

```ts
// app/docs/[[...slug]]/page.tsx
```

访问：

-   `/docs` → `params.slug === undefined`
-   `/docs/intro` → `params.slug === ['intro']`
-   `/docs/v1/install` → `params.slug === ['v1', 'install']`

* * *

## 📦 总结：动态路由命名方式一览表

| 类型       | 写法                          | 参数示例                               | 说明         |
| -------- | --------------------------- | ---------------------------------- | ---------- |
| 单个参数     | `[id]`                      | `{id: '123'}`                      | 匹配一个路径片段   |
| 多参数      | `[category]/[id]`           | `{category: 'books', id: '456'}`   | 多级路径       |
| 捕获所有路径   | `[...slug]`                 | `{slug: ['a', 'b', 'c'] }`         | 必须提供至少一个路径 |
| 可选捕获所有路径 | `[[...slug]]`               | `{slug: undefined}` 或 `['a', 'b']` | 可不带路径      |
| 嵌套路由     | `app/[id]/details/page.tsx` | `/users/123/details`               | 支持嵌套结构     |

* * *
