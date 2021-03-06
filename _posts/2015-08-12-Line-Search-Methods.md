<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>2015-08-09-line-searc-methods</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><p>{% include JB/setup %}</p>



<h1 id="line-search-methods">Line Search Methods</h1>

<p>Each iteration of a line search method computes a search direction <script type="math/tex" id="MathJax-Element-233">p_k</script>, and then decides how far to move along this direction. The iteration is given by</p>



<p><script type="math/tex; mode=display" id="MathJax-Element-264">x_{k+1}=x_k + \alpha_k p_k\tag{1}</script></p>

<p>The <script type="math/tex" id="MathJax-Element-265">\alpha_k</script> is the step length, and <script type="math/tex" id="MathJax-Element-266">p_k</script> is the search direction, which is a descent direction in most line search algorithms, which means that <script type="math/tex" id="MathJax-Element-267">p_k^T\nabla f_k < 0</script>,  because this property guarantees that the function <script type="math/tex" id="MathJax-Element-268">f</script> reduces along the direction. The search direction usually has the following form:</p>



<p><script type="math/tex; mode=display" id="MathJax-Element-327">p_k=-B_k^{-1}\nabla f_k\tag{2}</script></p>

<p>where <script type="math/tex" id="MathJax-Element-328">B_k</script> is a symmetric and nonsingular matrix. <script type="math/tex" id="MathJax-Element-329">B_k</script> is simply te identity matrix <script type="math/tex" id="MathJax-Element-330">I</script> in steepest descent method, exact Hessian <script type="math/tex" id="MathJax-Element-331">\nabla^2 f(x_k)</script>  in Newton’s method, and an approximation to the Hessian that is updated at every iteration by means of a low-rank formula. <br>
When <script type="math/tex" id="MathJax-Element-332">p_k</script> is defined by <script type="math/tex" id="MathJax-Element-333">(2)</script> and <script type="math/tex" id="MathJax-Element-334">B_k</script> is positive definite, we have </p>



<p><script type="math/tex; mode=display" id="MathJax-Element-335">p_k^T\nabla f_k =-\nabla f_k^TB_k^{-1}\nabla f_k < 0</script></p>

<p>, and therefore <script type="math/tex" id="MathJax-Element-336">p_k</script> is a descent direction.</p>

<h2 id="step-length">Step Length</h2>

<p>There is a trade-off in computing step length <script type="math/tex" id="MathJax-Element-337">\alpha_k</script>, we would like to get a step length <script type="math/tex" id="MathJax-Element-338">\alpha_k</script> that can substantially reduce the function <script type="math/tex" id="MathJax-Element-339">f</script> without spending too much time, i.e., we want to get a global minimizer of <script type="math/tex; mode=display" id="MathJax-Element-340">\phi(\alpha) = f(x_k + \alpha p_k), \alpha > 0</script>, but it’s usually too expensive to get the value(see the following figure). So in practice, we prefer to perform an inexact line search to identify a step length that achieves adequate reduction in <script type="math/tex" id="MathJax-Element-341">f</script> at minimal cost. <br>
<img src="https://lh3.googleusercontent.com/83JInNCEuvx_VL-dFjhTr0B0w3lcuaflvPT7STs-DQc=s0" alt="enter image description here" title="Screen Shot 2015-08-09 at 下午06.14.40.png"></p>

<p>Line Search Methods use the following strategies to get the adequate step length: <br>
   - A bracketing phase finds an interval containing desierable step lengths <br>
   - A bisection or interpolation phase computes a good step length within the interval <br>
There is different termination conditions for the line search methods, as showed in the following.</p>

<h3 id="the-wolfe-conditions">The Wolfe Conditions</h3>

<p>A popular inexact line search condition need that <script type="math/tex" id="MathJax-Element-390">\alpha_k</script> should first of all give <em>sufficient decrease</em> in the function <script type="math/tex" id="MathJax-Element-391">f</script>, as measured by the following inequality:</p>



<p><script type="math/tex; mode=display" id="MathJax-Element-436">f(x_k + \alpha p_k) \leq f(x_k) + c_1\alpha \nabla f_k^Tp_k</script></p>

<p>for some constant <script type="math/tex" id="MathJax-Element-437">c_1 \in (0, 1)</script>. In other words, the reduction in <script type="math/tex" id="MathJax-Element-438">f</script> should be proportional to both the step length <script type="math/tex" id="MathJax-Element-439">\alpha_k</script>, and the directional derivative <script type="math/tex" id="MathJax-Element-440">\nabla f_k^Tp_k \tag{3}</script>. The <script type="math/tex" id="MathJax-Element-441">(3)</script> inequality is sometimes called the <em>Armijo condition</em>. This sufficient decrease condition is showed in the following figure: <br>
<img src="https://lh3.googleusercontent.com/whPcGe44NLU06n5PFZF5QSk5yCL1w_l2KjvsRkwhcyA=s0" alt="enter image description here" title="Screen Shot 2015-08-09 at 下午06.14.59.png"> <br>
 The right-hand-side of <script type="math/tex" id="MathJax-Element-442">(3)</script>, which is a linear function, can be denoted by <script type="math/tex" id="MathJax-Element-443">l(\alpha)</script>. The acceptable intervals are showed in the figure. In practice, <script type="math/tex" id="MathJax-Element-444">c_1</script> is chosen to be quite small, say <script type="math/tex" id="MathJax-Element-445">c_1=10^{-4}</script> <br>
The sufficient decrease condition itself is not enough to ensure that te algorithms makes reasonable progress, because it’s satisfied for all sufficiently small value of <script type="math/tex" id="MathJax-Element-446">\alpha</script>, as showed in the above figure. To rule out such short steps, we introduce a second requirement, called the <em>curvature condition</em>, which requires <script type="math/tex" id="MathJax-Element-447">\alpha_k</script> to satisfy</p>



<p><script type="math/tex; mode=display" id="MathJax-Element-458">\nabla f(x_k + \alpha_k p_k)^Tp_k \geq c_2 \nabla f_k^Tp_k</script></p>

<p>for some constant <script type="math/tex" id="MathJax-Element-459">c_2 \in (c_1, 1)</script>, i.e., this conditions ensures that the slope of <script type="math/tex" id="MathJax-Element-460">\phi(\alpha_k)</script> is greater than <script type="math/tex" id="MathJax-Element-461">c_2</script> times the gradient <script type="math/tex" id="MathJax-Element-462">\phi'(0)</script>. <br>
This make sense since if te slope <script type="math/tex" id="MathJax-Element-463">\phi'(\alpha)</script> is strongly negative, we can reduce <script type="math/tex" id="MathJax-Element-464">f</script> significantly by moving further along the chosen direction. On the other hand, if the slope is only slightly negative or even positive, it’s a sign that we cannot expect much more decrease in <script type="math/tex" id="MathJax-Element-465">f</script> in this direction, so it might make sense to terminate the line search. The curvature condition is illustrated on the following figure <br>
<img src="https://lh3.googleusercontent.com/WdEEesKctLlcmcFaj-NNCvs5Ym4_81VhYvDt8ljpCPc=s0" alt="enter image description here" title="Screen Shot 2015-08-09 at 下午06.15.05.png"></p>

<p>The sufficient decrease condition and curvature condition are known collectively as the <em>Wolfe conditions</em>:</p>



<p><script type="math/tex; mode=display" id="MathJax-Element-468">f(x_k + \alpha)kp_k) \leq f(x_k) + c_1 \alpha_k\nabla f_k^Tp_k \tag{4}</script></p>



<p><script type="math/tex; mode=display" id="MathJax-Element-470">\nabla f(x_k + \alpha_kp_k)^Tp_k \geq c_2\nabla f_k^Tp_K \tag{5}</script></p>

<p>and is illustrated in the following figure: <br>
<img src="https://lh3.googleusercontent.com/cEQAj4MegLh_4tAEQ0flYpfm7FWXif4CKMBTDwfAnG4=s0" alt="enter image description here" title="Screen Shot 2015-08-09 at 下午06.15.12.png"></p>

<h3 id="the-goldstein-conditions">The Goldstein Conditions</h3>

<p>Like the Wolfe conditions, the <em>Goldstein conditions</em> also ensure that the step length <script type="math/tex" id="MathJax-Element-474">\alpha</script> achieves sufficient decrease while preventing <script type="math/tex" id="MathJax-Element-475">\alpha</script> from being too small:</p>



<p><script type="math/tex; mode=display" id="MathJax-Element-477">f(x_k) + (1-c)\alpha_k\nabla f_k^Tp_k \leq f(x_k + \alpha_kp_k) \leq f(x_k) + c\alpha_k \nabla f_k^Tp_k</script></p>

<p>and is showed in the following figure: <br>
<img src="https://lh3.googleusercontent.com/H2J6x6a_C4EFKjY6oCVcDe2y8iaC9W8V8yEQB-xtdPk=s0" alt="enter image description here" title="Screen Shot 2015-08-09 at 下午06.15.22.png"></p>

<h3 id="sufficient-decrease-and-backtracking">Sufficient Decrease and BackTracking</h3>

<p>As we have mentioned, the sufficient decrease condition itself is not enough to make reasonable progress along the given descent direction. However, we can use a so-called <em>BackTracking</em> approach to prevent <script type="math/tex" id="MathJax-Element-478">\alpha</script> from being too small:</p>

<blockquote>
  <p><strong>BackTracking Line Search</strong></p>
  
  <blockquote>
    <p>Choose <script type="math/tex" id="MathJax-Element-479">\bar{\alpha} > 0,  \rho, c \in (0, 1);</script>; set <script type="math/tex" id="MathJax-Element-480">\alpha \gets \bar{\alpha}</script> <br>
    <strong>repeat</strong> until <script type="math/tex" id="MathJax-Element-481">f(x_k + \alpha p_k) \leq f(x_k) + c\alpha \nabla f_k^Tp_k</script></p>
    
    <blockquote>
      <p><script type="math/tex" id="MathJax-Element-482">\alpha \gets \rho \alpha</script> <br>
      <strong>end </strong>(<strong>repeat</strong>) <br>
      Terminate with <script type="math/tex" id="MathJax-Element-483">\alpha_k = \alpha</script></p>
    </blockquote>
  </blockquote>
</blockquote>

<h2 id="rate-of-convergence">Rate of Convergence</h2></div></body>
</html>