# Course Content Manager Component Structure

This document outlines the component structure of the Course Content Manager web application.

## Top-Level Components

### App
The main application component that serves as the container for all other components.

### CourseHeader
Displays the course title, instructor information, and key course metadata.

### CourseNavigation
Provides navigation between different sections of the course.

## Content Section Components

### WhatYouWillLearn
- Lists learning objectives
- Displays expected outcomes
- Shows skill acquisition information

### CourseDescription
- Renders markdown content for course description
- Supports rich text formatting
- Includes course overview

### TargetAudience
- Displays information about intended audience
- Lists prerequisite knowledge
- Shows experience level expectations

### RequirementsList
- Displays technical requirements
- Lists software/hardware needs
- Shows preparatory materials

## Curriculum Components

### CurriculumSection
- Container for curriculum content
- Manages section state (expanded/collapsed)
- Handles section navigation

### SectionItem
- Individual curriculum item
- Displays title, duration, and completion status
- Manages content visibility

### LessonPlayer
- Video/audio playback interface
- Progress tracking
- Playback controls

## FAQ Components

### FaqAccordion
- Container for FAQ items
- Manages accordion state
- Handles search/filtering

### FaqItem
- Individual FAQ entry
- Handles expand/collapse logic
- Renders question and answer content

## Utility Components

### ProgressTracker
- Displays overall course progress
- Shows completion percentages
- Visualizes section completion status

### ExportTools
- Provides content export functionality
- Handles format conversion
- Manages download operations

### SearchInterface
- Course content search functionality
- Results highlighting
- Search history tracking

## Styling and Layout

The application uses a combination of:
- shadcn-ui components for consistent UI elements
- Tailwind CSS for responsive design
- Custom styling for course-specific elements

## Component Hierarchy

```
App
├── CourseHeader
├── CourseNavigation
├── WhatYouWillLearn
├── CourseDescription
├── TargetAudience
├── RequirementsList
├── CurriculumSection
│   ├── SectionItem
│   │   └── LessonPlayer
│   ├── SectionItem
│   └── SectionItem
├── FaqAccordion
│   ├── FaqItem
│   ├── FaqItem
│   └── FaqItem
├── ProgressTracker
├── ExportTools
└── SearchInterface
```

This structure ensures modularity, reusability, and maintainability of the application components.