# rationality-analyzer
Script for analyzing text rationality and emotional neutrality. Measures rationality coefficient (0-100%) by detecting emotional markers and logical patterns.
import re
import math
from collections import Counter

class RationalityAnalyzer:
    def __init__(self):
        # Emotional markers (words that reduce rationality)
        self.emotional_markers = {
            'positive': ['joy', 'happiness', 'love', 'excitement', 'passion', 
                        'hope', 'pride', 'satisfaction'],
            'negative': ['anger', 'fear', 'sadness', 'anxiety', 'disappointment',
                        'hate', 'irritation', 'resentment', 'annoyance'],
            'subjective': ['I think', 'I feel', 'in my opinion', 
                          'it seems to me', 'I believe', 'I hope']
        }
        
        # Logical connectors (increase rationality score)
        self.logical_connectors = [
            'therefore', 'thus', 'consequently', 'as a result',
            'if...then', 'since', 'because', 'due to',
            'as a consequence', 'in conclusion'
        ]
        
        # Scientific terms (increase rationality score)
        self.scientific_terms = [
            'analysis', 'research', 'data', 'result', 'method',
            'hypothesis', 'theory', 'experiment', 'observation', 'conclusion'
        ]
    
    def analyze(self, text):
        """Main analysis method"""
        text_lower = text.lower()
        
        # 1. Count emotional markers
        emotional_score = self._count_emotional_markers(text_lower)
        
        # 2. Count logical connectors
        logical_score = self._count_logical_connectors(text_lower)
        
        # 3. Count scientific terms
        scientific_score = self._count_scientific_terms(text_lower)
        
        # 4. Analyze sentence structure
        structure_score = self._analyze_sentence_structure(text)
        
        # 5. Calculate rationality coefficient
        rationality_score = self._calculate_rationality(
            emotional_score, logical_score, scientific_score, structure_score
        )
        
        return {
            'rationality_score': rationality_score,
            'emotional_markers': emotional_score,
            'logical_connectors': logical_score,
            'scientific_terms': scientific_score,
            'structure_quality': structure_score,
            'analysis': self._generate_analysis(rationality_score)
        }
    
    def _count_emotional_markers(self, text):
        """Count emotional markers in text"""
        count = 0
        for category in self.emotional_markers.values():
            for word in category:
                count += text.count(word)
        return count
    
    def _count_logical_connectors(self, text):
        """Count logical connectors in text"""
        count = 0
        for connector in self.logical_connectors:
            count += text.count(connector)
        return count
    
    def _count_scientific_terms(self, text):
        """Count scientific terms in text"""
        count = 0
        for term in self.scientific_terms:
            count += text.count(term)
        return count
    
    def _analyze_sentence_structure(self, text):
        """Analyze sentence structure quality"""
        # Split into sentences
        sentences = re.split(r'[.!?]+', text)
        sentences = [s.strip() for s in sentences if s.strip()]
        
        if not sentences:
            return 0
        
        # Calculate average sentence length
        avg_length = sum(len(s.split()) for s in sentences) / len(sentences)
        
        # Score: 10-25 words = optimal for rationality
        if 10 <= avg_length <= 25:
            return 1.0
        elif 5 <= avg_length < 10 or 25 < avg_length <= 40:
            return 0.7
        else:
            return 0.3
    
    def _calculate_rationality(self, emo, log, sci, struct):
        """Calculate rationality coefficient"""
        # Base score: less emotions = higher rationality
        base_score = 100 - (emo * 5)  # Each emotional marker = -5%
        
        # Bonuses for logic and science
        bonus = (log * 3) + (sci * 2) + (struct * 10)
        
        # Final score
        final_score = max(0, min(100, base_score + bonus))
        
        return round(final_score, 2)
    
    def _generate_analysis(self, score):
        """Generate textual analysis"""
        if score >= 80:
            return "Text is highly rational. Emotions are absent or minimal."
        elif score >= 60:
            return "Text is predominantly rational. Minor emotional elements present."
        elif score >= 40:
            return "Text contains moderate emotional content. Rationality is average."
        elif score >= 20:
            return "Text is emotionally charged. Rationality is low."
        else:
            return "Text is entirely emotional. Rationality is absent."

# Testing
if __name__ == "__main__":
    analyzer = RationalityAnalyzer()
    
    # Example 1: Rational text
    text1 = """
    Data analysis shows that the hypothesis is confirmed. 
    The experiment was conducted over 30 days. 
    Results demonstrate correlation between variables. 
    Therefore, the conclusion can be considered reliable.
    """
    
    # Example 2: Emotional text
    text2 = """
    I'm so excited about this discovery! It's incredible! 
    I feel like we're on the verge of a major breakthrough! 
    I love this approach!
    """
    
    # Example 3: Mixed text
    text3 = """
    I think this method works. 
    Data shows positive dynamics. 
    I really like this approach!
    """
    
    print("=" * 50)
    print("RATIONAL TEXT:")
    print("=" * 50)
    result1 = analyzer.analyze(text1)
    print(f"Rationality coefficient: {result1['rationality_score']}%")
    print(f"Analysis: {result1['analysis']}")
    print(f"Emotional markers: {result1['emotional_markers']}")
    print(f"Logical connectors: {result1['logical_connectors']}")
    print(f"Scientific terms: {result1['scientific_terms']}")
    
    print("\n" + "=" * 50)
    print("EMOTIONAL TEXT:")
    print("=" * 50)
    result2 = analyzer.analyze(text2)
    print(f"Rationality coefficient: {result2['rationality_score']}%")
    print(f"Analysis: {result2['analysis']}")
    print(f"Emotional markers: {result2['emotional_markers']}")
    print(f"Logical connectors: {result2['logical_connectors']}")
    print(f"Scientific terms: {result2['scientific_terms']}")
    
    print("\n" + "=" * 50)
    print("MIXED TEXT:")
    print("=" * 50)
    result3 = analyzer.analyze(text3)
    print(f"Rationality coefficient: {result3['rationality_score']}%")
    print(f"Analysis: {result3['analysis']}")
    print(f"Emotional markers: {result3['emotional_markers']}")
    print(f"Logical connectors: {result3['logical_connectors']}")
    print(f"Scientific terms: {result3['scientific_terms']}")
