# OpenDeepWiki Development Plan

## Project Overview

OpenDeepWiki is an evolution of DeepWiki designed to handle non-programming language content in repositories. It focuses on transforming repositories containing text documents, markdown, PDFs, and other knowledge assets into intelligent wikis that organize content by semantic relationships and temporal development.

## Core Concepts

### Content Classification
- **Consumed Content**: Research materials, references, inputs (books, papers, videos, etc.)
- **Produced Content**: Original writings, documents, outputs created by the user/organization
- **Seriation**: Temporal grouping of related concepts following Gwern's methodology
- **Semantic Linking**: Concept-based connections rather than API-style references

### Philosophy
Transform repositories into knowledge networks where later content is assumed to "call" earlier concepts, treating concept reuse like library dependencies in code.

## Current Architecture (Baseline)

### Backend (Python/FastAPI)
- `api/main.py` - Application entry point
- `api/api.py` - FastAPI server with CORS and routing
- `api/rag.py` - Retrieval Augmented Generation for chat
- `api/data_pipeline.py` - Repository processing and embeddings
- `api/config.py` - Multi-provider LLM configuration

### Frontend (Next.js/React)
- `src/app/page.tsx` - Main application interface
- `src/components/` - React components (Mermaid, Markdown, etc.)
- `src/contexts/LanguageContext.tsx` - Internationalization

### Key Features
- Multi-provider LLM support (Google, OpenAI, OpenRouter, Ollama)
- Real-time streaming chat with RAG
- Repository cloning and file processing
- Mermaid diagram generation
- Internationalization support

## Implementation Stages

### Stage 1: Minimal Proof of Concept (2-3 weeks)

#### New Components to Build
1. **Content Classification Engine** (`api/content_classifier.py`)
   - Document type detection (`.md`, `.txt`, `.pdf`, `.docx`)
   - Consumed vs Produced classification based on file paths and metadata
   - Date extraction from files/commits

2. **Enhanced Data Pipeline** (extend `api/data_pipeline.py`)
   - Support for non-code file types
   - Content extraction from PDFs, Word docs
   - Metadata extraction (creation dates, modification history)

3. **Semantic Concept Extraction** (`api/concept_extractor.py`)
   - Extract key concepts/themes from documents using LLMs
   - Build concept relationship graph
   - Identify recurring themes across documents

4. **Modified Wiki Structure** (extend existing WikiPage model in `api/api.py`)
   ```python
   class WikiPage(BaseModel):
       # Existing fields...
       content_type: Literal["consumed", "produced"]
       concepts: List[str]
       date_range: Dict[str, Optional[str]]  # {start: date, end: date}
       related_concepts: List[str]
   ```

#### Success Criteria
- Successfully classify documents as consumed/produced
- Extract basic concepts from text documents
- Generate wiki with content type indicators
- Demonstrate concept linking between documents

### Stage 2: Timeline and Seriation (4-6 weeks)

#### New Components to Build
1. **Temporal Organization** (`api/timeline_builder.py`)
   - Group content by time periods (daily/weekly/monthly/quarterly)
   - Identify theme development over time
   - Track concept evolution and refinement

2. **Frontend Timeline Component** (`src/components/TimelineView.tsx`)
   - Interactive timeline with zoom levels (day/week/month/quarter/year)
   - Theme bubbles clickable to navigate to content
   - Concept evolution visualization

3. **Seriation Implementation** (`api/seriation_engine.py`)
   - Cluster related documents by semantic similarity
   - Order within clusters by development/refinement chronology
   - Identify "parent" concepts and "derived" concepts

4. **Enhanced Wiki Interface** (modify existing components)
   - Add timeline navigation mode
   - Concept-based filtering and search
   - Visual indicators for content relationships

#### Success Criteria
- Working timeline view with clickable themes
- Automatic seriation of related documents
- Clear visualization of concept development over time
- Bidirectional navigation between timeline and wiki views

### Stage 3: Advanced Features (8-10 weeks)

#### New Components to Build
1. **Multi-format Document Processing** (`api/document_processors/`)
   - `pdf_processor.py` - PDF text extraction with layout preservation
   - `docx_processor.py` - Word document processing
   - `image_processor.py` - Image text extraction (OCR)
   - `video_processor.py` - Video transcript generation (future)

2. **Advanced Semantic Analysis** (enhance `api/concept_extractor.py`)
   - Concept dependency graphing
   - Influence tracking (later documents "calling" earlier concepts)
   - Knowledge gap identification
   - Cross-reference validation

3. **Interactive Visualizations** (`src/components/`)
   - `ConceptGraph.tsx` - Concept relationship network visualization
   - `InfluenceFlow.tsx` - Document influence and citation tracking
   - `KnowledgeMap.tsx` - Overall knowledge domain mapping

#### Success Criteria
- Support for PDF, Word, and image content
- Visual concept dependency graphs
- Clear influence tracking between documents
- Interactive knowledge exploration interface

### Stage 4: Production Features (12+ weeks)

#### New Components to Build
1. **Performance Optimization**
   - Incremental processing for large repositories
   - Concept relationship caching
   - Background processing for timeline updates
   - Database optimization for temporal queries

2. **Advanced Analytics** (`api/analytics/`)
   - Research productivity metrics
   - Knowledge development patterns
   - Concept coverage analysis
   - Learning pathway recommendations

3. **Export and Integration** (`api/exporters/`)
   - Generate publishable research summaries
   - Academic citation management
   - Integration with reference managers (Zotero, Mendeley)
   - LaTeX/academic format output

#### Success Criteria
- Handle repositories with 1000+ documents efficiently
- Generate publication-ready research summaries
- Integrate with existing academic workflows
- Provide actionable insights about knowledge development

## Technical Implementation Notes

### File Structure Extensions
```
api/
├── content_classifier.py      # NEW: Content type classification
├── concept_extractor.py       # NEW: Semantic concept extraction  
├── timeline_builder.py        # NEW: Temporal organization
├── seriation_engine.py        # NEW: Gwern-style seriation
├── document_processors/       # NEW: Multi-format document handling
│   ├── pdf_processor.py
│   ├── docx_processor.py
│   ├── image_processor.py
│   └── video_processor.py
├── analytics/                 # NEW: Knowledge analytics
│   ├── productivity_metrics.py
│   └── knowledge_patterns.py
└── exporters/                 # NEW: Export functionality
    ├── academic_exporter.py
    └── citation_manager.py

src/components/
├── TimelineView.tsx           # NEW: Timeline navigation
├── ConceptGraph.tsx           # NEW: Concept relationship visualization
├── ContentClassifier.tsx      # NEW: Content type management
├── InfluenceFlow.tsx          # NEW: Document influence tracking
└── KnowledgeMap.tsx           # NEW: Knowledge domain overview
```

### Database Schema Extensions
- Extend existing embeddings database with concept metadata
- Add temporal indexing for efficient timeline queries
- Store concept relationship graphs as adjacency lists
- Add content classification metadata to document records

### Development Approach
1. **Incremental Enhancement**: Build on existing robust foundation
2. **Backward Compatibility**: Maintain existing code wiki functionality
3. **Modular Design**: Each stage should be independently testable
4. **User-Driven**: Focus on clear value at each stage

## Testing Strategy

### Stage 1 Testing
- Unit tests for content classification accuracy
- Integration tests for concept extraction
- End-to-end tests for basic wiki generation

### Stage 2 Testing
- Timeline accuracy for various date ranges
- Seriation algorithm validation with known document sets
- UI/UX testing for timeline navigation

### Stage 3 Testing
- Multi-format document processing accuracy
- Concept graph correctness validation
- Performance testing with larger document sets

### Stage 4 Testing
- Load testing with 1000+ document repositories
- Export format validation
- Integration testing with external tools

## Success Metrics

### Quantitative
- Document processing accuracy (>95% for supported formats)
- Concept extraction precision and recall
- Timeline generation speed (<30 seconds for 100 documents)
- User engagement with generated wikis

### Qualitative
- User feedback on knowledge discovery capabilities
- Usefulness for research and academic workflows
- Clarity of concept relationships and evolution
- Overall user experience and intuitive navigation

## Dependencies and Requirements

### New Python Dependencies
- `PyPDF2` or `pdfplumber` for PDF processing
- `python-docx` for Word document processing
- `pytesseract` for OCR capabilities
- `networkx` for graph analysis
- `scikit-learn` for clustering and similarity
- `spacy` or `nltk` for advanced NLP

### Frontend Dependencies
- `d3.js` or `vis.js` for advanced visualizations
- `react-timeline-editor` for timeline components
- `cytoscape.js` for graph visualizations

### Infrastructure Considerations
- Increased storage requirements for document processing
- Enhanced computing requirements for semantic analysis
- Potential GPU acceleration for large-scale document processing

This plan provides a clear roadmap for transforming DeepWiki into OpenDeepWiki while maintaining the existing codebase's strengths and building incrementally toward a powerful knowledge organization system.