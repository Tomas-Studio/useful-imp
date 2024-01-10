## How to prerender routes in nuxt 

### Sample Code 

Add configuration with the hooks config, in your nuxt.config.ts file

```ts[nuxt.config.ts]
import { $fetch } from 'ofetch'
import type { Course, Degree, MultiResponse } from './types'

const BE_URL = 'https://url.com'

async function getDegreeRoutes() {
  const response = await $fetch<MultiResponse<Pick<Degree, 'slug'>>>(`${BE_URL}/api/degrees`)
  return response.data.map(degree => `/programs/graduate-degrees/${degree.attributes.slug}`)
}

async function getDCourseRoutes() {
  const response = await $fetch<MultiResponse<Pick<Course, 'slug'>>>(`${BE_URL}/api/courses`)
  return response.data.map(course => `/programs/short-courses/${course.attributes.slug}`)
}

const routeToPrerender = [
  '/',
  '/programs',
]

export default defineNuxtConfig({
  hooks: {
    'nitro:config': async function (nitroConfig) {
      if (nitroConfig.dev)
        return
      const degrees = await getDegreeRoutes()
      const courses = await getDCourseRoutes()
      nitroConfig.prerender!.routes!.push(...degrees, ...courses, ...routeToPrerender)
    },
  },
})
```
