---
name: ome-web-console
status: backlog
created: 2025-11-11T09:26:35Z
updated: 2025-11-11T10:07:31Z
progress: 0%
prd: .claude/prds/ome-web-console.md
github: https://github.com/sandeepsingh2bain/OpenManage-Enterprise-Console/issues/1
---

# Epic: ome-web-console

## Overview

The OME Web Console is a modern React-based dashboard that provides comprehensive visibility and management for Dell EMC OpenManage Enterprise infrastructure. The implementation leverages existing ome-dashboard application (already scaffolded with React 19, TypeScript, Vite, Tailwind CSS) and focuses on completing the core features: dashboard overview, device inventory, alert management, job monitoring, and reporting capabilities.

**Key Insight:** The application foundation already exists with proper tech stack, routing, and design system. This epic focuses on **enhancing and completing existing functionality** rather than building from scratch, significantly reducing implementation complexity.

## Architecture Decisions

### 1. Leverage Existing Foundation
**Decision:** Build upon the existing `ome-dashboard` application rather than create new infrastructure.

**Rationale:**
- React 19.2.0, TypeScript 5.9.3, Vite 7.2.2 already configured
- Tailwind CSS 4.1.17 with design system in place
- TanStack React Query 5.90.7 for data fetching
- React Router 7.9.5 for navigation
- Recharts 3.4.1 for visualizations

**Impact:** Reduces initial setup tasks and allows focus on feature implementation.

### 2. Mock-First API Strategy
**Decision:** Continue using mock API layer with structured data generators before backend integration.

**Rationale:**
- Allows frontend development to proceed independently
- PRD explicitly states Phase 1 uses mock data
- Real OME API integration deferred to Phase 2
- Existing `omeApi.ts` and `mockData.ts` pattern can be extended

**Impact:** No backend dependencies, faster iteration, clear migration path later.

### 3. Component-Driven Development
**Decision:** Build reusable UI components library following atomic design principles.

**Rationale:**
- Existing component structure (ui/, pages/, components/)
- Tailwind CSS enables rapid component styling
- Lucide React provides consistent iconography
- Promotes code reuse and maintainability

**Impact:** Consistent UI/UX, reduced code duplication, easier testing.

### 4. Performance Optimization via React Query
**Decision:** Use TanStack React Query for all data fetching with strategic caching.

**Rationale:**
- Already integrated in the stack
- Built-in caching, auto-refresh, and loading states
- Configured intervals: Dashboard (30s), Alerts (30s), Jobs (10s)
- Reduces unnecessary API calls and improves perceived performance

**Impact:** Meets <2s page load and <1s API response requirements.

### 5. Accessibility-First Design
**Decision:** Build WCAG 2.1 Level AA compliance into components from the start.

**Rationale:**
- PRD requires 4.5:1 color contrast, keyboard nav, ARIA labels
- Easier to build in than retrofit later
- Tailwind utilities support accessible patterns
- Screen reader compatibility critical for enterprise users

**Impact:** Meets compliance requirements, improves usability for all users.

## Technical Approach

### Frontend Components

**Core UI Components (Reusable):**
- StatsCard: Display metrics with trends (Total Servers, Alerts, Jobs, Health)
- DataTable: Sortable, filterable table for devices, alerts, jobs
- StatusBadge: Color-coded status indicators (Healthy, Warning, Critical, Offline)
- ProgressBar: Visual progress for running jobs
- ChartContainer: Wrapper for Recharts visualizations (pie charts, bar charts)
- SearchBar: Real-time search with filtering
- ExportButton: CSV export functionality
- RefreshIndicator: Auto-refresh timer and manual refresh button

**Page Components:**
- Dashboard.tsx: Overview with stats cards, health chart, recent alerts
- Devices.tsx: Device inventory table with search, filter, export
- Alerts.tsx: Alert list with summary cards, acknowledgment actions
- Jobs.tsx: Job monitoring with real-time status updates
- Reports.tsx: Report generation and export interface

**Layout Components:**
- DashboardLayout.tsx: Sidebar navigation, header, theme toggle
- ThemeContext.tsx: Dark/light mode state management

### State Management

**React Query for Server State:**
- `useDevices()`: Fetch and cache device inventory
- `useAlerts()`: Fetch alerts with auto-refresh (30s)
- `useJobs()`: Fetch jobs with auto-refresh (10s)
- `useDashboardStats()`: Aggregate statistics
- `useReports()`: Report generation status

**React Context for UI State:**
- Theme preferences (dark/light mode)
- Sidebar collapse state
- User preferences (refresh intervals, display options)

### Mock API Services

**Data Generators (mockData.ts extensions):**
- `generateDevices()`: 10-50 mock devices with realistic attributes
- `generateAlerts()`: Critical, Warning, Info alerts with timestamps
- `generateJobs()`: Running, Completed, Failed job states with progress
- `generateReportData()`: CSV-ready report structures

**API Service Layer (omeApi.ts extensions):**
- Simulated network delays (100-500ms) for realistic behavior
- OData-style query parameter support ($filter, $top, $skip, $orderby)
- Error scenarios for testing (5% random failure rate in dev mode)
- Acknowledge alert action (optimistic updates)

### Infrastructure

**Build & Deployment:**
- Vite for fast dev server and optimized production builds
- Code splitting by route (lazy loading)
- Asset optimization (minification, tree-shaking)
- Environment-based configuration (dev/prod)

**Performance Monitoring:**
- React DevTools for component profiling
- Lighthouse CI for performance audits
- Web Vitals tracking (LCP, FID, CLS)
- Bundle size monitoring

**Browser Support:**
- Target: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- Modern ES6+ features (no legacy transpilation needed)
- CSS Grid and Flexbox for layouts
- LocalStorage for client-side preferences

## Implementation Strategy

### Phase 1: Core Infrastructure Enhancement (Already Mostly Complete)
**Goal:** Ensure foundation is solid and design system is consistent.

**Activities:**
- Validate existing routing and navigation
- Standardize component patterns and naming
- Configure React Query defaults and error boundaries
- Establish TypeScript interfaces for all data models

**Risk Mitigation:**
- Existing codebase review to identify gaps
- Ensure design tokens are properly defined in Tailwind config

### Phase 2: Feature Implementation (Primary Development Phase)
**Goal:** Complete all five core feature areas with full functionality.

**Activities:**
- Implement Dashboard with stats, charts, and recent alerts
- Build Device Inventory with search, filter, sort, export
- Create Alert Management with acknowledge and auto-refresh
- Develop Job Monitoring with real-time updates
- Add Reports interface with CSV generation

**Risk Mitigation:**
- Start with Dashboard (highest visibility) to validate patterns
- Build reusable components first (StatsCard, DataTable)
- Implement error boundaries for graceful degradation

### Phase 3: Polish & Optimization
**Goal:** Ensure performance targets are met and UX is refined.

**Activities:**
- Performance profiling and optimization (memo, lazy loading)
- Accessibility audit and WCAG 2.1 AA compliance verification
- Cross-browser testing (Chrome, Firefox, Safari, Edge)
- Responsive design validation (mobile, tablet, desktop)
- User acceptance testing with personas

**Risk Mitigation:**
- Automated Lighthouse CI in build pipeline
- Manual testing checklist for accessibility
- Performance budget alerts (<2s load, <500ms nav)

### Testing Approach

**Unit Testing:**
- Component logic with React Testing Library
- Utility functions and data transformers
- Mock data generators

**Integration Testing:**
- React Query hooks with mock API
- User workflows (search, filter, export)
- Alert acknowledgment and job monitoring

**E2E Testing (Optional for Phase 1):**
- Critical user paths (login → dashboard → devices)
- Data export functionality
- Auto-refresh behavior

**Performance Testing:**
- Lighthouse audits (target: 90+ performance score)
- Load testing with large data sets (10k devices, 1k alerts)
- Memory leak detection with React DevTools

## Task Breakdown Preview

High-level task categories that will be created:

- [ ] **Foundation & Components:** Implement reusable UI component library (StatsCard, DataTable, StatusBadge, ChartContainer, etc.)
- [ ] **Dashboard Page:** Build overview with stats cards, health distribution chart, recent alerts list, and auto-refresh
- [ ] **Device Inventory:** Implement device list table with search, filter, sort, export CSV functionality
- [ ] **Alert Management:** Create alert list with summary cards, acknowledge actions, and configurable auto-refresh
- [ ] **Job Monitoring:** Develop job list with real-time updates, progress bars, and animated status indicators
- [ ] **Reports Interface:** Add report generation UI with export capabilities for all report types
- [ ] **Mock API Enhancement:** Extend mock data generators and API service layer for all endpoints
- [ ] **Theme & Layout:** Complete dark/light mode implementation and responsive layout refinements
- [ ] **Performance Optimization:** Implement code splitting, React.memo, and caching strategies to meet <2s load target
- [ ] **Accessibility & Testing:** WCAG 2.1 AA compliance verification, cross-browser testing, and user acceptance validation

**Total Estimated Tasks:** 10 major tasks covering all implementation areas.

## Dependencies

### External Dependencies
- **None for Phase 1:** Using mock API, no external services required
- **Future (Phase 2):** OpenManage Enterprise API availability, authentication service

### Internal Dependencies
- **Design System:** Tailwind config with Dell EMC brand colors (already defined)
- **Mock Data:** Realistic data generators for devices, alerts, jobs (needs extension)
- **Component Library:** Reusable UI components (to be built, but patterns exist)

### Technical Prerequisites
- Node.js 20.19+ or 22.12+ (already in use)
- Modern browser with ES6+ support (target audience has this)
- 1280x720 minimum screen resolution (standard for enterprise users)

### Risk Areas
- **Data Volume Handling:** Need to validate performance with 10k devices, 1k alerts
- **Auto-Refresh Performance:** Multiple concurrent intervals (10s, 30s) may impact memory
- **CSV Export:** Large data sets may cause browser memory issues (need streaming or chunking)

## Success Criteria (Technical)

### Performance Benchmarks
- **Initial Page Load:** < 2 seconds (95th percentile)
- **Time to Interactive:** < 3 seconds
- **Subsequent Navigation:** < 500ms
- **API Response Time:** < 1 second (mock API should be < 200ms)
- **Bundle Size:** < 500KB gzipped (initial load)
- **Lighthouse Score:** 90+ for performance, accessibility, best practices

### Quality Gates
- **TypeScript Strict Mode:** No type errors in codebase
- **ESLint:** Zero warnings or errors
- **Component Coverage:** 100% of UI components have props interfaces
- **Accessibility:** WCAG 2.1 Level AA compliance (4.5:1 contrast, keyboard nav, ARIA)
- **Browser Support:** Verified on Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- **Responsive Design:** Functional on 640px (mobile), 768px (tablet), 1024px+ (desktop)

### Acceptance Criteria
- **Dashboard loads with all stats and charts within 5 seconds**
- **Device search returns results in real-time (<100ms perceived latency)**
- **Alert acknowledgment provides immediate visual feedback**
- **Job status updates visible within 10 seconds of state change**
- **CSV export generates file for 1000+ rows without browser hang**
- **Auto-refresh does not cause visible UI flicker or layout shift**
- **Dark/light mode toggle applies instantly without page reload**
- **Keyboard navigation works for all interactive elements**

### User Experience Validation
- **Alex (Senior SysAdmin):** Can view critical metrics within 5 seconds of login
- **Sam (NOC Operator):** Can acknowledge alerts with single click
- **Jordan (IT Manager):** Can export device inventory with <5 clicks

## Estimated Effort

### Overall Timeline
- **Foundation & Components:** 1-2 days (enhance existing, build reusables)
- **Dashboard Page:** 2-3 days (stats, charts, recent alerts)
- **Device Inventory:** 2-3 days (table, search, filter, export)
- **Alert Management:** 2-3 days (list, acknowledge, auto-refresh)
- **Job Monitoring:** 2 days (list, progress, real-time updates)
- **Reports Interface:** 1-2 days (UI for report generation)
- **Mock API Enhancement:** 1-2 days (extend generators and service layer)
- **Theme & Layout:** 1 day (polish dark/light mode, responsive tweaks)
- **Performance Optimization:** 2-3 days (profiling, memo, lazy loading, testing)
- **Accessibility & Testing:** 2-3 days (WCAG audit, cross-browser, UAT)

**Total Estimated Effort:** 15-22 days (3-4 weeks) for one full-time developer

### Resource Requirements
- **Frontend Developer:** 1 senior or 1-2 mid-level developers
- **Designer (Optional):** Design system already defined, minimal design input needed
- **QA/Testing:** 2-3 days for comprehensive testing (can be done by developer in Phase 1)

### Critical Path Items
1. **Reusable Component Library:** Must be completed first (all pages depend on this)
2. **Mock API Enhancement:** Required for all data-driven features
3. **Dashboard Page:** High visibility, validates architectural decisions
4. **Performance Optimization:** Must meet <2s load time target before launch

### Risk Buffer
- **20% contingency:** Add 3-4 days for unforeseen issues (data volume, browser quirks, polish)
- **User feedback cycle:** Add 2-3 days for iterations based on UAT

**Total with Buffer:** 20-29 days (4-6 weeks)

## Notes on Simplification

### Leveraging Existing Work
- **ome-dashboard already exists** with proper React 19 + TypeScript + Vite setup
- **Design system is defined** in Tailwind config with Dell EMC brand colors
- **Routing and navigation** structure is in place
- **TanStack React Query** is already integrated

### What's NOT Being Built (Phase 1)
- Real backend API integration (using mocks)
- Authentication/authorization (future phase)
- Advanced analytics or AI/ML features
- Mobile native apps (web-only, but responsive)
- Third-party integrations (ServiceNow, Jira, etc.)

### Opportunities for Further Simplification
- **Use CSS-only animations** instead of JavaScript for performance
- **Limit initial data sets** to 100 devices, 50 alerts for faster prototyping
- **Defer advanced filtering** (just basic text search + status filter in Phase 1)
- **CSV export via browser download API** (no server-side processing needed)

This epic represents a pragmatic, achievable implementation plan that builds on existing work and delivers core functionality in 4-6 weeks with minimal complexity.

## Tasks Created

- [ ] #2 - Foundation & Components - Reusable UI Component Library (parallel: true)
- [ ] #6 - Mock API Enhancement - Data Generators and Service Layer (parallel: true)
- [ ] #9 - Dashboard Page - Overview with Stats and Charts (parallel: false)
- [ ] #10 - Device Inventory - Table with Search, Filter, and Export (parallel: false)
- [ ] #12 - Alert Management - List with Acknowledge and Auto-Refresh (parallel: false)
- [ ] #13 - Job Monitoring - Real-Time Updates with Progress Bars (parallel: false)
- [ ] #14 - Reports Interface - Generation and Export UI (parallel: false)
- [ ] #15 - Theme & Layout - Dark/Light Mode and Responsive Design (parallel: true)
- [ ] #16 - Performance Optimization - Code Splitting and Caching (parallel: false)
- [ ] #17 - Accessibility & Testing - WCAG Compliance and Cross-Browser Validation (parallel: false)

Total tasks: 10
Parallel tasks: 3
Sequential tasks: 7
Estimated total effort: 130-178 hours
