# algorithm7
#include <bits/stdc++.h>
using namespace std;

struct Node {
    char ch;
    int freq;
    Node *left, *right;
    Node(char c, int f) : ch(c), freq(f), left(nullptr), right(nullptr) {}
};

struct Compare {
    bool operator()(Node* a, Node* b) {
        return a->freq > b->freq;
    }
};

void buildCodeMap(Node* root, string code, unordered_map<char, string>& codeMap) {
    if (!root) return;
    if (root->ch != '$') codeMap[root->ch] = code;
    buildCodeMap(root->left, code + "0", codeMap);
    buildCodeMap(root->right, code + "1", codeMap);
}

void HuffmanCoding(string text) {
    unordered_map<char, int> freq;
    for (char c : text) freq[c]++;

    priority_queue<Node*, vector<Node*>, Compare> minHeap;
    for (auto p : freq) minHeap.push(new Node(p.first, p.second));

    cout << "Building Huffman Tree:\n";
    int step = 1;
    while (minHeap.size() > 1) {
        Node *left = minHeap.top(); minHeap.pop();
        Node *right = minHeap.top(); minHeap.pop();

        cout << "Step " << step++ << ": Combine ";
        cout << (left->ch == '$' ? "*" : string(1, left->ch)) << "(" << left->freq << ") + ";
        cout << (right->ch == '$' ? "*" : string(1, right->ch)) << "(" << right->freq << ") = ";
        cout << (left->freq + right->freq) << "\n";

        Node *internal = new Node('$', left->freq + right->freq);
        internal->left = left;
        internal->right = right;
        minHeap.push(internal);
    }

    Node* root = minHeap.top();
    unordered_map<char, string> codeMap;
    buildCodeMap(root, "", codeMap);

    cout << "\nCharacter | Frequency | Code   | Bit Size\n";
    cout << "----------------------------------------------\n";

    int compressedBits = 0;
    for (auto p : freq) {
        char ch = p.first;
        int f = p.second;
        string code = codeMap[ch];
        int bits = f * code.length();
        compressedBits += bits;
        cout << "   " << ch << "      |     " << f << "      |  " << code << "   | "
             << f << " x " << code.length() << " = " << bits << "\n";
    }

    string encoded = "";
    for (char c : text) encoded += codeMap[c];

    int originalBits = text.length() * 8;
    int tableBits = freq.size() * 8;

    cout << "\nOriginal String: " << text << "\n";
    cout << "Encoded Binary : " << encoded << "\n";
    cout << "\nUncompressed Size : " << text.length() << " x 8 = " << originalBits << " bits\n";
    cout << "Frequency Table   : " << freq.size() << " x 8 = " << tableBits << " bits\n";
    cout << "Compressed Data   : " << compressedBits << " bits\n";
    cout << "Final Transmission Size = " << tableBits << " + " << text.length()
         << " + " << compressedBits << " = " << (tableBits + text.length() + compressedBits) << " bits\n";
}

int main() {
    string input = "BBCCAAAADDDCCCCAAAABBBBCCCCDDDDAAACCCCBBAADDDCCACACAC";
    HuffmanCoding(input);
    return 0;
}
