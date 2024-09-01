# PID 对象
```C++
#include <iostream>

class PID {
public:
    // 构造函数，初始化PID参数
    PID(double kp, double ki, double kd, double integral_limit = 100.0)
        : kp_(kp), ki_(ki), kd_(kd), integral_limit_(integral_limit),
          prev_error_(0.0), integral_(0.0), derivative_(0.0) {}

    // 更新PID控制器，输入当前误差error和时间间隔dt
    double update(double error, double dt) {
        // 计算微分项，考虑防止除以零的情况
        derivative_ = (error - prev_error_) / dt;
        
        // 积分项累积，添加限制避免积分饱和
        integral_ += error * dt;
        integral_ = std::clamp(integral_, -integral_limit_, integral_limit_);
        
        // 计算总输出
        double output = kp_ * error + ki_ * integral_ + kd_ * derivative_;
        
        // 更新前一次误差
        prev_error_ = error;
        
        return output;
    }

    // 设置PID参数
    void setGains(double kp, double ki, double kd) {
        kp_ = kp;
        ki_ = ki;
        kd_ = kd;
    }

    // 重置PID控制器状态
    void reset() {
        prev_error_ = 0.0;
        integral_ = 0.0;
        derivative_ = 0.0;
    }

private:
    double kp_;      // 比例增益
    double ki_;      // 积分增益
    double kd_;      // 微分增益
    double integral_limit_;  // 积分限幅值
    double prev_error_;     // 上一时刻误差
    double integral_;       // 积分项
    double derivative_;     // 微分项
};

// 示例使用
int main() {
    PID pid_controller(0.5, 0.1, 0.02);  // 初始化PID参数
    double error = 1.0;  // 假设初始误差
    double dt = 0.1;     // 时间间隔，例如每100ms更新一次

    // 进行PID控制循环
    for (int i = 0; i < 100; ++i) {
        double control_output = pid_controller.update(error, dt);
        std::cout << "Control Output: " << control_output << std::endl;
        // 在实际应用中，control_output会用于调整系统，这里简化处理
    }

    return 0;
}
```