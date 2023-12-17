# Animated Loading Text

## Screen video
https://github.com/narzullaevnurbek/loading_text_animation/assets/119044390/8bc7f390-7f25-4772-b05f-e82d090b0506

# Essentials
```swift
private let stackView: UIStackView = {
        let stackView = UIStackView()
        stackView.axis = .horizontal
        stackView.distribution = .equalSpacing
        stackView.spacing = 10
        return stackView
    }()
    
    let startButton: UIButton = {
        let button = UIButton(type: .system)
        button.setTitle("Start", for: .normal)
        button.setTitleColor(.systemBlue, for: .normal)
        button.addTarget(self, action: #selector(startLoading), for: .touchUpInside)
        button.titleLabel?.font = .systemFont(ofSize: 20)
        return button
    }()
    
    let stopButton: UIButton = {
        let button = UIButton(type: .system)
        button.setTitle("Stop", for: .normal)
        button.addTarget(self, action: #selector(stopLoading), for: .touchUpInside)
        button.setTitleColor(.red, for: .normal)
        button.titleLabel?.font = .systemFont(ofSize: 20)
        return button
    }()

    private var timer = Timer()

    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        
        [stackView, startButton, stopButton].forEach { item in
            view.addSubview(item)
        }
        
        let loading = "LOADING"
        
        for char in loading {
            let label = UILabel()
            label.textColor = .black
            label.font = .systemFont(ofSize: 25, weight: .semibold)
            label.text = "\(char)"
            self.stackView.addArrangedSubview(label)
        }
        
        
        layout()
        
    }

    private func layout() {
        stackView.snp.makeConstraints {
            $0.top.equalTo(view.safeAreaLayoutGuide.snp.top).offset(50)
            $0.centerX.equalTo(view.snp.centerX)
            $0.height.equalTo(30)
        }
        
        startButton.snp.makeConstraints {
            $0.centerX.equalTo(view.snp.centerX)
            $0.top.equalTo(stackView.snp.bottom).offset(Constants.padding + 20)
            $0.leading.equalToSuperview()
            $0.trailing.equalToSuperview()
        }
        
        stopButton.snp.makeConstraints {
            $0.centerX.equalTo(view.snp.centerX)
            $0.top.equalTo(startButton.snp.bottom).offset(Constants.padding)
            $0.leading.equalToSuperview()
            $0.trailing.equalToSuperview()
        }
    }

    @objc
    func startLoading() {
        self.animate(0) // to start animation from the first UILabel in the UIStackView
        self.timer.fire() //  intended to immediately run the timer after button click
    }
    
    
    @objc
    func stopLoading() {
        self.timer.invalidate() // to pause the animation
    }
```
1. Created UIStackView to keep each character of "LOADING" text, as UILabel separately.
2. System type "Start" and "Stop" buttons.
3. The last one is the timer. It serves to keep repetition of animations on.

# Core-Logic
```swift
    private func animate(_ index: Int) { // index parameter gets the starting character point of animation.
        // array of views within the UIStackView
        let subViews = self.stackView.subviews

        // timer to continuously run animation every 0.5 seconds
        timer = Timer.scheduledTimer(withTimeInterval: 0.5, repeats: true, block: { timer in
            // animation function that pulls the characters up and down in 0.4 seconds
            UIView.animate(withDuration: 0.4) {
                // moves the character up
                subViews[index].transform = CGAffineTransform(translationX: 0, y: -15)

                if index == subViews.count - 1 { // if the character is last one in the UIStackView
                    self.animate(0) // animation goes to the initial point
                } else {
                    self.animate(index + 1) // otherwise animation jumps to the next character
                }
                
            } completion: { _ in // in the completion of moving up
                // we should pull current character to its original position
                UIView.animate(withDuration: 0.4) {
                    subViews[index].transform = CGAffineTransform(translationX: 0, y: 0)
                }
                // then pause the animation of current character until its next queue
                timer.invalidate()
            }
        })
        
    }
```
### Used: UIKit, SnapKit, CoreAnimation
