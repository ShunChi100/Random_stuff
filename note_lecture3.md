## Lecture 3
MDS, from Mark Schmidt's lecture

### Maximum Likelihood Estimation
Maximum likelihood estimation (MLE) is the procedure of finding the values of $\Theta$ for a given data set which make the likelihood function a maximum [1]. Here the likelihood function is simply the probability that the event leading to the given data happens. Taking coin flipping as an example, we have observed three heads and two tails in five trials of the same coin. The number of heads in a given number of trials forms a binomial distribution with probability $p$. Here $p$ is the probability of a head in a toss. We are not sure if the coin is fair or not. However, we want to estimate the most likely $p$ for this coin based on our observations. MLE can help here. The procedure is the following:

* Calculate the likelihood as a function distribution parameter $\Theta$. Here $\Theta$ is $p$ in the binomial distribution case
$$\mathcal{L}(p) = (_{5}^{3})p^3(1-p)^2 = 10p^3(1-p)^2$$

* Take the negative logarithm of the likelihood function
$$\displaystyle{\mathrm{argmin}_{p}}-\log(\mathcal{L}(p)).$$
The benefits of using the negative logarithm are
  - avoiding overflow or underflow
  - changing the product of probabilities to the sum of log probabilities.

* Find the $p$ that minimize $-\log(\mathcal{L}(p))$.

The solution is $\sqrt{\frac{3}{5}}$. It seems not like a fair coin. (For a more careful conclusion, we need to estimate the confidence interval.)


#### MLE for regression
In statistics, the regression of a parametric model assumes the response variable $Y$ following certain probability distribution. For example, linear regression assumes $Y$ following a normal distribution and logistic regression assumes $Y$ following a binomial distribution.

The problem setup is the following:

* We have a set of data (observations).
* We make an assumption on the distribution of the response variable.
* We want to find the parameters $\theta$ that the set of observations are most likely to happen.

In general cases, let $f(Y|\Theta)$ be the probability distribution for the response variable. $\Theta$ is the distribution parameter that is a function of independent variables $x_i$ and parameters $W$. For example, for the linear regression case, $\Theta$ is the mean and given by $\Theta = W^TX$. Given a set of observations (a sample) $D$ with $n$ pairs of $[y_i, x_i]$, then the likelihood function is
$$\mathcal{L}(D|\theta) = \prod_{i}f(y_i|\theta_i) = \prod_{i}f(y_i|w,x_i).$$ The negative logarithm of it is
$$-\log(\mathcal{L}(D|w)) = -\sum_{i}\log(f(y_i|w,x_i)).$$
The MLE objective function is
$$\mathrm{argmin}_{w} -\log(\mathcal{L}(D|w)) = \mathrm{argmin}_{w}-\sum_{i}\log(f(y_i|w,x_i)).$$

##### Linear regression
In linear regression, we assume a linear relationship with an error term
$$Y = WX + \xi,$$
where the intercept term is included by adding a dummy variable to $X$. $\xi$ follows a Gaussian distribution.

So the response variable follows a Gaussian distribution
$$Y \sim N(\Theta = WX, \sigma) = \frac{1}{\sqrt{2\pi \sigma^2}}e^{-\frac{(Y-WX)^2}{2\sigma^2}},$$
where $\Theta$ is the mean and $\sigma$ is the standard deviation for the noise $\xi$.

Follow the MLE objective function
$$\mathrm{argmin}_{w} \left[  -\log(\mathcal{L}(D|w))\right] = \mathrm{argmin}_{w}\left[-\sum_{i}\log(N(\theta = w^T x_i, \sigma))\right]$$  
$$ = \mathrm{argmin}_{w}\left[-\sum_{i}\log(\frac{1}{\sqrt{2\pi \sigma^2}}e^{-\frac{(y_i-w^Tx_i)^2}{2\sigma^2}})\right]$$
$$ = \mathrm{argmin}_{w}\left[-\sum_{i}\left[\log\left(\frac{1}{\sqrt{2\pi \sigma^2}}\right)+\log\left(e^{-\frac{(y_i-w^Tx_i)^2}{2\sigma^2}}\right)\right]\right]$$
$$ = const. + \frac{1}{2\sigma^2}\mathrm{argmin}_{w}\sum_{i}(y_i-w^Tx_i)^2$$

So the MLE becomes a problem to find $w$ that minimize the sum of squared errors
$$\sum_{i}(y_i-w^Tx_i)^2$$

So now we can see that the origin of object (loss) function for linear regression is MLE of a Gaussian distributed response variable.

##### Logistic regression
In logistic regression, the response variable $Y$ is binary and follows a binomial distribution. Let's set the binary values to be -1 and 1 [2]. The parameter of the model is $p$, the probability for $Y = 1$. $p$ is a function of independent variables $X$ and parameters $W$. One of the most popular choice is the sigmoid function
$$p = h(W^TX) = \frac{1}{1+e^{-W^TX}}$$
Therefore, for an observation with $y_i = 1$, we have
$$P(y_i = 1) = p = h(w^Tx_i)$$
since $y_i = 1$, so $h(w^Tx_i) = h(y_iw^Tx_i)$. For an observation with $y_i = -1$, we have
$$P(y_i = -1) = 1 - p = 1 - h(w^Tx_i) = 1- \frac{1}{1+e^{-w^Tx_i}} = \frac{1}{1+e^{w^Tx_i}} = h(-w^Tx_i)$$
since $y_i = -1$, so $h(-w^Tx_i) = h(y_iw^Tx_i)$. So for each observation $y_i$, we can write the probability in a clean way
$$P(y_i) = h(y_iw^Tx_i).$$


Follow the general MLE objective function
$$\mathrm{argmin}_{w} \left[  -\log(\mathcal{L}(D|w))\right] = \mathrm{argmin}_{w}\left[-\sum_{i}\log\left(P(y_i|w, x_i)\right)\right]$$
$$= \mathrm{argmin}_{w}\left[-\sum_{i}\log\left(h(y_iw^Tx_i)\right)\right]$$
$$= \mathrm{argmin}_{w}\left[-\sum_{i}\log\left(\frac{1}{1+e^{-y_iw^Tx_i}}\right)\right]$$
$$= \mathrm{argmin}_{w}\left[\sum_{i}\log\left(1+e^{-y_iw^Tx_i}\right)\right]$$

So the MLE becomes a problem to find $w$ that minimize the objective (loss) function
$$\sum_{i}\log\left(1+e^{-z_i}\right)$$ where $z_i = y_iw^Tx_i$. ($x_i$ includes a dummy variable for intercept.)

### Maximum a posterior estimation
Some introduction here.
y_iw^Tx_i
#### MAP for regression
Given a set of data $D$, what is the distribution of parameters $W$? We can use the Bayesian rule
$$P(w|D) = \frac{P(D|w)P(w)}{P(D)}$$
here $P(D)$ is independent from $w$, so only the numerator is important for determining $w$.

The problem setup is the following:

* We have observed some data.
* We make an assumption on the distribution of the response variable.
* We make an assumption on the prior distribution of parameter $w$ based on our knowledge.
* We want to find $w$ that corresponds to the maximum of the posterior given the observed data.

In the general case, let $f(Y|w, X)$ be the probability distribution for the response variable and $g(w)$ be the prior distribution of parameter $w$. Given a set of observations (a sample) $D$ with $n$ pairs of $[y_i, x_i]$, then the posterior is
$$P(w|D) = \prod_{i}f(y_i|w, x_i)g(w).$$ The negative logarithm is
$$-\log(P(w|D)) = -\log\left(\prod_{i}f(y_i|w, x_i)g(w)\right)$$
$$\ \ \ \ \  \ \ \ \ \ \ \  = -\log\left(\prod_{i}f(y_i|w, x_i)\right) - \log\left(g(w)\right)$$
$$ = -\sum_{i}\log\left(f(y_i|w, x_i)\right) - \log(g(w))$$
The MAP problem becomes
$$\mathrm{argmin}_{w} -\log(P(w|D))= \mathrm{argmin}_{w} \left[-\sum_{i}\log(f(y_i|w,x_i)) - \log(g(w))\right].$$

#### MAP for linear regression with a Gaussian prior
Suppose the prior of $w$ is a Gaussian distribution $N(0, \sigma_w)$. This indicates that $w$ are likely around zero and extremely large values in $w$ are very unlikely to happen.

Similar to MLE for linear regression, we insert the probability density functions for the response variable and parameters prior in the MAP objective function
$$\mathrm{argmin}_{w} -\log(P(w|D)) = \mathrm{argmin}_{w}\left[-\sum_{i}\log(N(\theta = w^T x_i, \sigma)) - \log(N(0, \sigma_w))\right]$$  
$$ = \mathrm{argmin}_{w}\left[-\sum_{i}\log(\frac{1}{\sqrt{2\pi \sigma^2}}e^{-\frac{(y_i-w^Tx_i)^2}{2\sigma^2}}) - \log(\frac{1}{\sqrt{2\pi \sigma_w^2}}e^{-\frac{w^2}{2\sigma_w^2}})\right]$$
$$ = \mathrm{argmin}_{w}\left[-\sum_{i}\left[\log\left(\frac{1}{\sqrt{2\pi \sigma^2}}\right)+\log\left(e^{-\frac{(y_i-w^Tx_i)^2}{2\sigma^2}}\right)\right]- \left[\log\left(\frac{1}{\sqrt{2\pi \sigma_w^2}}\right)+\log\left(e^{-\frac{w^2}{2\sigma_w^2}}\right)\right]\right]$$
$$ = const. + \frac{1}{2\sigma^2}\mathrm{argmin}_{w}\left[\sum_{i}(y_i-w^Tx_i)^2 + \frac{\sigma^2}{\sigma_w^2}w^2 \right]$$

So MAP for linear regression with a Gaussian prior becomes a problem to find $w$ that minimize the sum of squared errors with a $L_2$ regularization
$$\min\sum_{i}(y_i-w^Tx_i)^2 + \lambda w^2$$
where $\lambda = \frac{\sigma^2}{\sigma_w^2}$. Thus the origin of object (loss) function for linear regression with $L_2$ regularization is MAP estimation of $w$ with a Gaussian prior.

The question is how to choose the hyperparameter $\lambda$. Typically, we choose the $\lambda$ using cross-validation. In this sense, the hyperparameter is determined by data.   

#### MAP for robust regression with a Laplace prior
We still have the model
$$Y = WX + \xi,$$
where the intercept is taken into account by including as a dummy variable in $X$. Here the noise $\xi$ is from a Laplace distribution [3] instead of a Gaussian distribution. Laplace distribution is a _heavy tail distribution_, so extreme values away from the mean are more likely than Gaussian distribution. Outliers can be taken care more appropriately in this distribution.

With these assumption, $Y$ is given by
$$Y \sim \mathrm{Laplace}(\theta = WX, b) = \frac{1}{2b}e^{-\frac{|Y-WX|}{b}},$$
where $\theta$ is the mean and $b$ is the diversity for the noise term $\xi$.

We also assume the parameter prior is a Laplace distribution
$$g(w) = \mathrm{Laplace}(0, b_w) = \frac{1}{2b_w}e^{-\frac{|w|}{b_w}}$$

Then the MAP objective function
$$\mathrm{argmin}_{w} -\log(P(w|D)) = \mathrm{argmin}_{w}\left[-\sum_{i}\log(\mathrm{Laplace}(w^Tx_i, b)) - \log(\mathrm{Laplace}(0, b_w))\right]$$  
$$ = \mathrm{argmin}_{w}\left[-\sum_{i}\log( \frac{1}{2b}e^{-\frac{|y_i-w^Tx_i|}{b}}) - \log(\frac{1}{2b_w}e^{-\frac{|w|}{b_w}})\right]$$
$$ = const. + \frac{1}{b}\mathrm{argmin}_{w}\left[\sum_{i}|y_i-w^Tx_i| + \frac{b}{b_w}|w| \right]$$

So MAP for robust regression with a Laplace prior becomes a problem to find $w$ that minimize the sum of absolute errors with a $L_1$ regularization
$$\sum_{i}|y_i-w^Tx_i| + \lambda |w|$$
where $\lambda = \frac{b}{b_w}$.

[1]: [Maximum Likelihood](http://mathworld.wolfram.com/MaximumLikelihood.html)
[2]: One can always transform any binary output to be -1 and 1.
[3]: [Laplace distribution](https://en.wikipedia.org/wiki/Laplace_distribution)

************
* Add iid discussion.
* The problem setup, suppose we know $w$.
* The assumption on the probability distribution is what we believe the world.
* The choice of MLE or MAP is what we believe how the world works.
* Laplace distribution: no longer that unbelievable that have a point far away from the line.
************
