import { definePlugin, logger, storage, settings } from "@api";
import { setCustomStatus } from "@utils/Status";

interface AnimationStep {
    text: string;
    emoji_name?: string;
    emoji_id?: string;
    timeout?: number;
}

const defaultAnimation: AnimationStep[] = [
    { text: "Animating...", emoji_name: "✨", timeout: 3000 },
    { text: "With Vencord!", emoji_name: "🌀", timeout: 3000 },
];

let animation: AnimationStep[] = storage.get("animation") ?? defaultAnimation;
let timeout: number = storage.get("timeout") ?? 3000;
let randomize: boolean = storage.get("randomize") ?? false;
let loop: number | undefined;
let stopped = false;

function setAnimatedStatus(step: AnimationStep) {
    setCustomStatus({
        text: step.text,
        emoji_name: step.emoji_name,
        emoji_id: step.emoji_id,
    });
}

function animationLoop(i = 0) {
    if (animation.length === 0 || stopped) return;
    i %= animation.length;
    setAnimatedStatus(animation[i]);
    const time = animation[i].timeout ?? timeout;
    loop = window.setTimeout(() => {
        animationLoop(randomize ? Math.floor(Math.random() * animation.length) : i + 1);
    }, time);
}

function stopAnimation() {
    stopped = true;
    if (loop) clearTimeout(loop);
    setCustomStatus(null);
}

export default definePlugin({
    name: "AnimatedStatus",
    description: "Animate your Discord status with custom steps.",
    authors: [{name: "toluschr"}, {name: "SirSlender"}, {name: "Vencord Port: Copilot"}],

    settings: () => (
        <>
            <settings.Switch
                title="Randomize Steps"
                note="Randomize the order of the animation."
                value={randomize}
                onChange={v => {
                    randomize = v;
                    storage.set("randomize", v);
                }}
            />
            <settings.NumberInput
                title="Default Step Duration (ms)"
                note="How long each step is shown (overwritten by per-step)."
                value={timeout}
                min={1000}
                onChange={v => {
                    timeout = v;
                    storage.set("timeout", v);
                }}
            />
            <settings.TextArea
                title="Animation Steps (JSON)"
                note={`Format: ${JSON.stringify(defaultAnimation, null, 2)}`}
                value={JSON.stringify(animation, null, 2)}
                onChange={v => {
                    try {
                        animation = JSON.parse(v);
                        storage.set("animation", animation);
                    } catch (err) {
                        logger.error("Invalid animation JSON", err);
                    }
                }}
            />
        </>
    ),
    onLoad() {
        stopped = false;
        animationLoop();
    },
    onUnload() {
        stopAnimation();
    }
});
