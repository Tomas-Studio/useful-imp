## How to prerender routes in nuxt 

### Sample Code 

Add configuration with the hooks config, in your nuxt.config.ts file

```ts[nuxt.config.ts]
import { $fetch } from 'ofetch'
import type { Course, Degree, MultiResponse } from './types'

const BE_URL = 'https://url.com'

// functions to get routes to prerender
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
  // Add configuration in hooks under nitro:config
  hooks: {
    'nitro:config': async function (nitroConfig) {
      if (nitroConfig.dev)
        return

      // get routes to prerender
      const degrees = await getDegreeRoutes()
      const courses = await getDCourseRoutes()

      // push the routes to prerender in the routes array
      nitroConfig.prerender!.routes!.push(...degrees, ...courses, ...routeToPrerender)
    },
  },
})
```
