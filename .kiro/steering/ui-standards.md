# UI Development Standards

## Technology Stack

- **Framework**: React 18+
- **Language**: TypeScript (strict mode enabled)
- **Build Tool**: Vite or Create React App
- **State Management**: Context API for simple state, Redux Toolkit for complex state
- **Styling**: CSS Modules or Styled Components (team decision required)

## Component Architecture

### Component Structure
- Use functional components with hooks
- Follow single responsibility principle
- Keep components under 250 lines; refactor if larger
- Separate presentational and container components

### File Organization
```
src/
├── components/
│   ├── common/          # Reusable UI components
│   ├── features/        # Feature-specific components
│   └── layouts/         # Page layouts
├── hooks/               # Custom React hooks
├── services/            # API integration layer
├── utils/               # Helper functions
├── types/               # TypeScript type definitions
└── assets/              # Static assets
```

### Naming Conventions
- Components: PascalCase (e.g., `UserProfile.tsx`)
- Hooks: camelCase with 'use' prefix (e.g., `useAuth.ts`)
- Utilities: camelCase (e.g., `formatDate.ts`)
- Constants: UPPER_SNAKE_CASE (e.g., `API_BASE_URL`)

## Code Quality Standards

### TypeScript Usage
- Enable strict mode in tsconfig.json
- Avoid `any` type; use `unknown` if type is truly unknown
- Define interfaces for all props and state
- Use type inference where possible

### Component Best Practices
- Use React.memo() for expensive components
- Implement proper error boundaries
- Use lazy loading for route-based code splitting
- Avoid inline function definitions in JSX (use useCallback)

### State Management
- Keep state as local as possible
- Lift state only when necessary
- Use Context API for theme, auth, and user preferences
- Use Redux Toolkit for complex application state

## API Integration

### Service Layer
- Create dedicated service files for API calls
- Use axios or fetch with proper error handling
- Implement request/response interceptors for auth tokens
- Handle loading and error states consistently

### Example Pattern
```typescript
// services/userService.ts
export const userService = {
  getUser: async (id: string): Promise<User> => {
    const response = await apiClient.get(`/users/${id}`);
    return response.data;
  }
};
```

## Authentication

- **Provider**: Authentik
- Implement token-based authentication (JWT)
- Store tokens securely (httpOnly cookies preferred)
- Implement automatic token refresh
- Handle 401 responses globally with redirect to login
- Clear all auth state on logout

## Security Standards

### PII Handling
- Never log PII data to console or error tracking
- Mask sensitive data in UI (e.g., SSN, credit cards)
- Implement field-level encryption for sensitive form data
- Follow GDPR/privacy requirements for data display

### Input Validation
- Validate all user inputs on client side
- Sanitize data before rendering (prevent XSS)
- Use proper encoding for URL parameters
- Implement CSRF protection for forms

## Performance Standards

### Loading Performance
- Target First Contentful Paint (FCP) < 1.5s
- Target Time to Interactive (TTI) < 3.5s
- Implement code splitting for routes
- Lazy load images and heavy components

### Runtime Performance
- Avoid unnecessary re-renders (use React DevTools Profiler)
- Debounce search inputs and expensive operations
- Implement virtual scrolling for large lists
- Optimize bundle size (target < 250KB initial load)

## Accessibility (a11y)

- Follow WCAG 2.1 Level AA standards
- Use semantic HTML elements
- Implement proper ARIA labels and roles
- Ensure keyboard navigation works for all interactive elements
- Maintain color contrast ratios (4.5:1 for normal text)
- Test with screen readers (NVDA, JAWS, or VoiceOver)

## Testing Standards

### Unit Testing
- Use React Testing Library (not Enzyme)
- Test user behavior, not implementation details
- Aim for 70%+ coverage on critical paths
- Mock external dependencies (API calls, third-party libraries)

### Integration Testing
- Test complete user flows
- Use MSW (Mock Service Worker) for API mocking
- Test error scenarios and edge cases

### E2E Testing
- Use Playwright or Cypress for critical user journeys
- Focus on happy paths and critical business flows
- Run E2E tests in CI/CD pipeline

## Logging and Monitoring

### Client-Side Logging
- Use structured logging (e.g., winston, pino)
- Log levels: ERROR, WARN, INFO, DEBUG
- Never log sensitive data (passwords, tokens, PII)
- Implement error boundary logging

### Error Tracking
- Integrate with error tracking service (Sentry, Rollbar)
- Include user context (non-PII) with errors
- Track JavaScript errors and unhandled promise rejections
- Monitor performance metrics

## Build and Deployment

### Build Process
- Use environment variables for configuration
- Separate configs for dev, UAT, and prod
- Minify and optimize assets in production builds
- Generate source maps for debugging (store securely)

### Code Quality Gates
- ESLint with strict rules (no warnings in production)
- Prettier for code formatting
- Pre-commit hooks with Husky
- Type checking must pass before merge

## Documentation

- Document complex components with JSDoc comments
- Maintain Storybook for component library
- Keep README updated with setup instructions
- Document environment variables and their purposes

## Version Control

- Follow feature branch workflow
- Meaningful commit messages (Conventional Commits)
- Squash commits before merging to main
- Require code review before merge
