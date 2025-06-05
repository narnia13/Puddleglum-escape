import React from "react";

export default function App() {
  const canvasRef = React.useRef(null);
  const [pos, setPos] = React.useState({ x: 50, y: 200 });
  const [vel, setVel] = React.useState({ x: 0, y: 0 });
  const [keys, setKeys] = React.useState({ left: false, right: false, up: false });
  const [swordLevel, setSwordLevel] = React.useState(1);
  const gravity = 0.5;
  const groundY = 200;

  const doSomethingGood = () => {
    setSwordLevel((level) => Math.min(level + 1, 5));
  };

  React.useEffect(() => {
    const handleKeyDown = (e) => {
      setKeys((k) => ({
        ...k,
        left: e.key === "ArrowLeft" ? true : k.left,
        right: e.key === "ArrowRight" ? true : k.right,
        up: e.key === "ArrowUp" ? true : k.up,
      }));
      if (e.key === "g") doSomethingGood();
    };
    const handleKeyUp = (e) => {
      setKeys((k) => ({
        ...k,
        left: e.key === "ArrowLeft" ? false : k.left,
        right: e.key === "ArrowRight" ? false : k.right,
        up: e.key === "ArrowUp" ? false : k.up,
      }));
    };
    window.addEventListener("keydown", handleKeyDown);
    window.addEventListener("keyup", handleKeyUp);
    return () => {
      window.removeEventListener("keydown", handleKeyDown);
      window.removeEventListener("keyup", handleKeyUp);
    };
  }, []);

  React.useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext("2d");

    const draw = () => {
      ctx.clearRect(0, 0, 300, 300);
      ctx.fillStyle = "#3e4e3c";
      ctx.fillRect(0, 0, 300, 300);
      ctx.fillStyle = "#4f3d2f";
      ctx.fillRect(0, groundY + 30, 300, 70);
      ctx.fillStyle = "#88c070";
      ctx.fillRect(pos.x, pos.y, 20, 40);
      ctx.fillStyle = "#cccccc";
      ctx.fillRect(pos.x + 22, pos.y + 10, 3, swordLevel * 10);
      ctx.fillStyle = "#2a8032";
      ctx.fillRect(200, 210, 40, 20);
      ctx.fillStyle = "#a38bdc";
      ctx.fillRect(120, 210, 15, 30);
      ctx.fillStyle = "white";
      ctx.font = "12px sans-serif";
      ctx.fillText("Sword Level: " + swordLevel, 10, 20);
    };

    const update = () => {
      let newX = pos.x;
      let newY = pos.y;
      let newVelY = vel.y;
      if (keys.left) newX -= 2;
      if (keys.right) newX += 2;
      if (keys.up && pos.y >= groundY) newVelY = -8;
      newVelY += gravity;
      newY += newVelY;
      if (newY >= groundY) {
        newY = groundY;
        newVelY = 0;
      }
      setPos({ x: newX, y: newY });
      setVel({ x: 0, y: newVelY });
    };

    let animationFrameId;
    const loop = () => {
      update();
      draw();
      animationFrameId = requestAnimationFrame(loop);
    };

    loop();
    return () => cancelAnimationFrame(animationFrameId);
  }, [pos, vel, keys, swordLevel]);

  return <canvas ref={canvasRef} width={300} height={300} />;
}
