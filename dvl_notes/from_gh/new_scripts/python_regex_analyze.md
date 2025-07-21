import re
from collections import defaultdict

def analyze_to_regex(text_line):
    """
    Analyzes characters in a line of text and returns a regex pattern that would match similar strings.
    
    Args:
        text_line (str): The input text line to analyze
        
    Returns:
        str: A regex pattern that would match strings with similar character patterns
    """
    if not text_line:
        return ""
    
    # Analyze character classes
    pattern = []
    char_categories = defaultdict(str)
    
    for char in text_line:
        if char.isupper():
            char_categories[char] = '[A-Z]'
        elif char.islower():
            char_categories[char] = '[a-z]'
        elif char.isdigit():
            char_categories[char] = r'\d'
        elif char.isspace():
            char_categories[char] = r'\s'
        else:
            # Escape special regex characters
            if char in r'.^$*+?{}[]\|()':
                char_categories[char] = re.escape(char)
            else:
                char_categories[char] = char
    
    # Build pattern with character classes and quantifiers
    i = 0
    n = len(text_line)
    
    while i < n:
        current_char = text_line[i]
        current_category = char_categories[current_char]
        j = i + 1
        
        # Count consecutive characters of the same category
        while j < n and char_categories[text_line[j]] == current_category:
            j += 1
        
        count = j - i
        
        # Add to pattern with appropriate quantifier
        if count == 1:
            pattern.append(current_category)
        else:
            pattern.append(f"{current_category}{{{count}}}")
        
        i = j
    
    return ''.join(pattern)


# Example usage:
if __name__ == "__main__":
    test_strings = [
        "Hello123",
        "ABC-123-xyz",
        "2023-06-15",
        "user@example.com",
        "The quick brown fox"
    ]
    
    for test in test_strings:
        pattern = analyze_to_regex(test)
        print(f"Original: {test}")
        print(f"Pattern:  {pattern}")
        print(f"Match:    {bool(re.fullmatch(pattern, test))}")
        print("-" * 40)
