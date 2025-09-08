# Super Field - Attachment List

A multi-file upload component for Budibase applications supporting multiple attachments with file management, validation, and bulk operations.

## 🚀 Features

### Multiple File Upload

- **Bulk Upload**: Upload multiple files simultaneously
- **File Management**: Add, remove, and reorder attachments
- **Drag & Drop**: Intuitive multi-file drag-and-drop interface
- **File Preview**: Thumbnail and file information display
- **Default Values**: Pre-populated attachment lists

### File Organization

- **List Display**: Clear file list with metadata
- **File Validation**: Size, type, and count restrictions
- **Template Formatting**: Custom file display formatting
- **Event Handling**: On change events with file list context
- **State Management**: Disabled and readonly modes

### User Experience

- **Visual Feedback**: Upload progress and status indicators
- **Error Handling**: Clear error messages for failed uploads
- **Auto-focus**: Automatic focus for file selection
- **Debounced Input**: Performance optimization for large lists
- **Help Text**: Guidance for file requirements and limits

### Advanced Features

- **Custom Buttons**: Configurable action buttons for file operations
- **Inline Icons**: Visual indicators for attachment lists
- **Bulk Operations**: Select and manage multiple files
- **File Metadata**: Size, type, and upload date information
- **Conditional Logic**: Dynamic behavior based on file list state

### Styling & Layout

- **Flexible Positioning**: Label placement options
- **Field Modes**: Form input or inline editing
- **Size Configuration**: Adjustable component width
- **Theme Integration**: Consistent with Budibase design

## 📝 Usage Instructions

### Basic Setup

1. Add the Super Field - Attachment List component to your form
2. Bind to an attachment array field
3. Configure file validation rules (size, type, count)
4. Set help text for file requirements

### Advanced Configuration

- **File Limits**: Set maximum file count and sizes
- **Validation**: Configure comprehensive file validation
- **Custom Buttons**: Add action buttons for file management
- **Events**: Attach actions to file list changes

### Common Use Cases

- **Document Collections**: Multiple document uploads
- **Media Galleries**: Image and media file collections
- **Evidence Files**: Supporting documentation sets
- **Portfolio Uploads**: Multiple work samples
- **Data Imports**: Bulk file processing

## 🔧 Configuration Options

| Setting          | Type          | Description                       |
| ---------------- | ------------- | --------------------------------- |
| Field            | Attachment    | Attachment array field binding    |
| Label            | String        | Display label text                |
| Placeholder      | String        | Upload guidance text              |
| Default Value    | Array         | Pre-populated file list           |
| Formatted Value  | Template      | File display formatting           |
| Help Text        | String        | Help/instruction text             |
| Validation       | Rules         | File validation (size/type/count) |
| Autofocus        | Boolean       | Auto-focus on load                |
| Debounced        | Boolean       | Enable input debouncing           |
| Disabled         | Boolean       | Disable file uploads              |
| Read Only        | Boolean       | Read-only mode                    |
| Clear Value Icon | Boolean       | Show clear icon button            |
| Add Inline Icon  | Boolean       | Enable additional icon            |
| Icon             | Icon          | Inline icon selection             |
| Add Buttons      | Boolean       | Enable custom buttons             |
| Buttons          | Configuration | Custom action buttons             |
| Field Mode       | Select        | Form or inline input style        |
| Label Position   | Select        | Label placement                   |
| Size             | Number        | Component width span              |

## 📋 Events

### On Change

Triggered when files are added, removed, or modified.

**Context:**

- `value`: Array of uploaded file information
- `field`: The bound field information

## 🎨 Styling

The component integrates with Budibase's styling system:

- **File List**: Organized file display layout
- **Upload Zone**: Visual multi-file drop area
- **Action Buttons**: Styled file management controls
- **Progress Indicators**: Upload progress visualization

## 🔍 Best Practices

- Set clear file count and size limits
- Provide specific file type requirements
- Use custom buttons for advanced file operations
- Consider mobile users for file management
- Implement proper error handling for uploads
- Test with various file types and sizes
