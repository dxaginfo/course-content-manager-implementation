# Component Structure Documentation

## Overview

The Course Content Manager application follows a modular component architecture that promotes reusability, maintainability, and separation of concerns. This document outlines the main components and their relationships.

## Component Hierarchy

```
App
├── Layout
│   ├── Header
│   ├── Sidebar
│   └── Footer
├── CourseView
│   ├── CourseHeader
│   ├── CourseNavigation
│   └── CourseContent
│       ├── WhatYouWillLearn
│       ├── CourseDescription
│       ├── TargetAudience
│       ├── RequirementsList
│       ├── CurriculumSection
│       │   ├── SectionItem
│       │   └── LessonPlayer
│       └── FaqAccordion
└── AdminView
    ├── ContentManager
    ├── UserProgress
    └── SettingsPanel
```

## Key Components

### Layout Components

#### Header
- **Purpose**: Main navigation header for the application
- **Props**: `user`, `isAdmin`, `onLogout`
- **State**: `isMenuOpen`

#### Sidebar
- **Purpose**: Secondary navigation for course sections
- **Props**: `sections`, `activeSectionId`, `onSectionChange`
- **State**: `isExpanded`

#### Footer
- **Purpose**: Contains site links and copyright information
- **Props**: `copyrightYear`, `links`

### Course View Components

#### CourseHeader
- **Purpose**: Displays course title, instructor, and ratings
- **Props**: `title`, `instructor`, `rating`
- **State**: None

#### CourseNavigation
- **Purpose**: Tab navigation for different course content sections
- **Props**: `sections`, `activeSection`, `onSectionChange`
- **State**: `activeTab`

#### CourseContent
- **Purpose**: Container for the main course content
- **Props**: `courseData`, `activeSection`
- **State**: `isLoading`

#### WhatYouWillLearn
- **Purpose**: Displays learning objectives
- **Props**: `objectives`
- **State**: None

#### CourseDescription
- **Purpose**: Renders formatted course description
- **Props**: `description`, `format`
- **State**: None

#### TargetAudience
- **Purpose**: Shows who the course is intended for
- **Props**: `targetAudience`
- **State**: None

#### RequirementsList
- **Purpose**: Lists course prerequisites and requirements
- **Props**: `requirements`
- **State**: None

#### CurriculumSection
- **Purpose**: Container for course curriculum sections
- **Props**: `sections`, `onSectionClick`
- **State**: `expandedSections`

#### SectionItem
- **Purpose**: Represents a single lesson within a section
- **Props**: `lesson`, `isCompleted`, `duration`, `onClick`
- **State**: `isExpanded`

#### LessonPlayer
- **Purpose**: Displays lesson content (video, text, etc.)
- **Props**: `lessonData`, `onComplete`, `onProgress`
- **State**: `progress`, `isPlaying`

#### FaqAccordion
- **Purpose**: Expandable FAQ section
- **Props**: `faqItems`, `searchQuery`
- **State**: `expandedItems`, `filteredItems`

### Admin View Components

#### ContentManager
- **Purpose**: Interface for managing course content
- **Props**: `courseData`, `onUpdate`
- **State**: `editingSection`, `unsavedChanges`

#### UserProgress
- **Purpose**: Displays user progress statistics
- **Props**: `userProgressData`
- **State**: `selectedMetric`, `dateRange`

#### SettingsPanel
- **Purpose**: Course settings configuration
- **Props**: `settings`, `onSettingsChange`
- **State**: `currentSettings`

## Component Implementation Details

### UI Component Library

The application uses shadcn-ui, providing these core components:

- `Accordion` - Used for FAQ and expandable curriculum sections
- `Tabs` - Used for course navigation
- `Card` - Used for section items
- `Button` - Used throughout the application
- `Dialog` - Used for confirmations and detailed views
- `Progress` - Used for progress tracking
- `DropdownMenu` - Used for actions and filtering

### Styling Implementation

All components use Tailwind CSS with a consistent approach:

```jsx
// Example component with Tailwind styling
const SectionItem = ({ title, duration, isCompleted }) => {
  return (
    <div className="flex items-center justify-between p-4 border-b border-gray-200 hover:bg-gray-50 transition-colors">
      <div className="flex items-center gap-3">
        {isCompleted ? (
          <CheckCircleIcon className="h-5 w-5 text-green-500" />
        ) : (
          <CircleIcon className="h-5 w-5 text-gray-300" />
        )}
        <span className="font-medium">{title}</span>
      </div>
      <span className="text-sm text-gray-500">{duration}</span>
    </div>
  );
};
```

### State Management

For application state management, we use React Context with hooks:

```jsx
// CourseContext.jsx
const CourseContext = createContext();

export const CourseProvider = ({ children }) => {
  const [courseData, setCourseData] = useState(null);
  const [userProgress, setUserProgress] = useState({});
  const [activeSection, setActiveSection] = useState('description');
  
  // Load course data on mount
  useEffect(() => {
    // Fetch course data
  }, []);
  
  const markLessonComplete = (lessonId) => {
    // Update user progress
  };
  
  return (
    <CourseContext.Provider 
      value={{ 
        courseData, 
        userProgress, 
        activeSection, 
        setActiveSection,
        markLessonComplete 
      }}
    >
      {children}
    </CourseContext.Provider>
  );
};

export const useCourse = () => useContext(CourseContext);
```

## Component Examples

### CurriculumSection Component

```tsx
import { useState } from 'react';
import { Accordion, AccordionContent, AccordionItem, AccordionTrigger } from '@/components/ui/accordion';
import { SectionItem } from './SectionItem';
import { useCourse } from '@/context/CourseContext';

interface CurriculumSectionProps {
  sections: {
    id: string;
    title: string;
    duration: string;
    lessons: {
      id: string;
      title: string;
      duration: string;
      type: 'video' | 'text' | 'quiz';
    }[];
  }[];
}

export function CurriculumSection({ sections }: CurriculumSectionProps) {
  const [expandedSections, setExpandedSections] = useState<string[]>([sections[0]?.id]);
  const { userProgress, markLessonComplete } = useCourse();
  
  const handleSectionToggle = (sectionId: string) => {
    setExpandedSections(prev => 
      prev.includes(sectionId) 
        ? prev.filter(id => id !== sectionId)
        : [...prev, sectionId]
    );
  };
  
  return (
    <div className="space-y-6">
      <h2 className="text-2xl font-bold">Curriculum</h2>
      
      <Accordion type="multiple" value={expandedSections}>
        {sections.map(section => (
          <AccordionItem key={section.id} value={section.id}>
            <AccordionTrigger 
              onClick={() => handleSectionToggle(section.id)}
              className="flex justify-between py-4"
            >
              <div className="flex justify-between w-full pr-4">
                <span>{section.title}</span>
                <span className="text-sm text-gray-500">{section.duration}</span>
              </div>
            </AccordionTrigger>
            <AccordionContent>
              <div className="divide-y">
                {section.lessons.map(lesson => (
                  <SectionItem 
                    key={lesson.id}
                    lesson={lesson}
                    isCompleted={userProgress[lesson.id]?.completed || false}
                    onClick={() => markLessonComplete(lesson.id)}
                  />
                ))}
              </div>
            </AccordionContent>
          </AccordionItem>
        ))}
      </Accordion>
    </div>
  );
}
```

### FaqAccordion Component

```tsx
import { useState, useEffect } from 'react';
import { Accordion, AccordionContent, AccordionItem, AccordionTrigger } from '@/components/ui/accordion';
import { Input } from '@/components/ui/input';
import { Search } from 'lucide-react';

interface FaqItem {
  id: string;
  question: string;
  answer: string;
  category: string;
}

interface FaqAccordionProps {
  faqItems: FaqItem[];
}

export function FaqAccordion({ faqItems }: FaqAccordionProps) {
  const [searchQuery, setSearchQuery] = useState('');
  const [expandedItems, setExpandedItems] = useState<string[]>([]);
  const [filteredItems, setFilteredItems] = useState<FaqItem[]>(faqItems);
  
  useEffect(() => {
    if (!searchQuery.trim()) {
      setFilteredItems(faqItems);
      return;
    }
    
    const query = searchQuery.toLowerCase();
    const filtered = faqItems.filter(
      item => 
        item.question.toLowerCase().includes(query) || 
        item.answer.toLowerCase().includes(query)
    );
    
    setFilteredItems(filtered);
    
    // Auto-expand items when searching
    if (filtered.length > 0 && searchQuery) {
      setExpandedItems(filtered.map(item => item.id));
    }
  }, [searchQuery, faqItems]);
  
  const handleItemToggle = (itemId: string) => {
    setExpandedItems(prev => 
      prev.includes(itemId) 
        ? prev.filter(id => id !== itemId)
        : [...prev, itemId]
    );
  };
  
  return (
    <div className="space-y-6">
      <h2 className="text-2xl font-bold">Frequently Asked Questions</h2>
      
      <div className="relative">
        <Input
          placeholder="Search FAQs..."
          value={searchQuery}
          onChange={(e) => setSearchQuery(e.target.value)}
          className="pl-10"
        />
        <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400 h-4 w-4" />
      </div>
      
      {filteredItems.length === 0 ? (
        <p className="text-center text-gray-500 py-8">No FAQs match your search query.</p>
      ) : (
        <Accordion type="multiple" value={expandedItems}>
          {filteredItems.map(item => (
            <AccordionItem key={item.id} value={item.id}>
              <AccordionTrigger 
                onClick={() => handleItemToggle(item.id)}
                className="text-left"
              >
                {item.question}
              </AccordionTrigger>
              <AccordionContent>
                <div className="prose max-w-none">
                  {item.answer}
                </div>
              </AccordionContent>
            </AccordionItem>
          ))}
        </Accordion>
      )}
    </div>
  );
}
```

## Accessibility Considerations

All components are built with accessibility in mind:

- Proper semantic HTML elements
- ARIA labels and roles where appropriate
- Keyboard navigation support
- Color contrast compliance
- Focus management for modals and dialogs

## Performance Optimization

The component architecture includes these performance optimizations:

- Memoization of expensive components with `React.memo`
- Virtualization for long lists using `react-window`
- Code splitting with dynamic imports
- Lazy loading of components with `React.lazy`
- Image optimization with next-gen formats and proper sizing

## Future Component Enhancements

Planned component improvements:

1. Add dark mode support for all components
2. Implement internationalization (i18n) 
3. Add additional accessibility features
4. Improve mobile responsiveness
5. Add animation and transition effects